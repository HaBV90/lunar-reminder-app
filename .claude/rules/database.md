# Database Rules

## PostgreSQL Conventions

### Naming

- Tables: `snake_case`, plural (`events`, `event_groups`, `user_settings`)
- Columns: `snake_case` (`created_at`, `user_id`, `lunar_day`)
- Indexes: `idx_{table}_{columns}` (`idx_events_user_id`, `idx_events_solar_date`)
- Constraints: `fk_{table}_{ref_table}`, `uq_{table}_{columns}`, `chk_{table}_{column}`

### Required Columns (Every Table)

```sql
id          UUID PRIMARY KEY DEFAULT uuid_generate_v4()
created_at  TIMESTAMP WITH TIME ZONE DEFAULT NOW()
updated_at  TIMESTAMP WITH TIME ZONE DEFAULT NOW()
deleted_at  TIMESTAMP WITH TIME ZONE  -- soft delete, nullable
```

### Data Types

- IDs: UUID (never auto-increment integers)
- Dates: `TIMESTAMP WITH TIME ZONE` for absolute times
- Lunar dates: Store as integers (`lunar_day`, `lunar_month`, `lunar_year`, `is_leap_month`)
- Booleans: `BOOLEAN DEFAULT FALSE`
- Strings: `VARCHAR(n)` with explicit length, never unbounded `TEXT` unless justified
- Enums: `VARCHAR` column + TypeScript enum (not DB-level enum for easier migration)

---

## TypeORM Entity Rules

### Decorators

```typescript
@Entity('table_name')           // Always explicit table name
@PrimaryGeneratedColumn('uuid') // UUID, never increment
@Column({ name: 'column_name' }) // Explicit snake_case column name
@CreateDateColumn({ name: 'created_at' })
@UpdateDateColumn({ name: 'updated_at' })
@DeleteDateColumn({ name: 'deleted_at' })
```

### Relations

- Always define both sides of a relation
- Use `{ onDelete: 'CASCADE' }` for dependent entities
- Use `{ onDelete: 'SET NULL' }` for optional references
- Always add `@JoinColumn({ name: 'fk_column' })` with explicit name
- Never use `eager: true` in entity definition; load relations explicitly in queries

### Indexes

```typescript
@Index('idx_events_user_id')
@Index('idx_events_solar_date')
@Index(['userId', 'type'])  // Composite index
```

- Index foreign keys
- Index columns used in WHERE/ORDER BY
- Index composite keys for common query patterns

---

## Migration Rules

### Naming

Format: `{timestamp}-{descriptive-name}.ts`

```
1706000001-create-users-table.ts
1706000002-create-events-table.ts
1706000003-add-lunar-fields-to-events.ts
```

### Requirements

1. Every migration MUST be reversible (`up` and `down`)
2. Never modify a committed/merged migration
3. New changes = new migration file
4. Test both `up` and `down` before committing
5. Migrations run in order; never reorder

### Template

```typescript
export class CreateEventsTable1706000002 implements MigrationInterface {
  public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`
      CREATE TABLE events (
        id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
        user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
        name VARCHAR(200) NOT NULL,
        type VARCHAR(50) NOT NULL,
        created_at TIMESTAMPTZ DEFAULT NOW(),
        updated_at TIMESTAMPTZ DEFAULT NOW(),
        deleted_at TIMESTAMPTZ
      )
    `);
    await queryRunner.query(`CREATE INDEX idx_events_user_id ON events(user_id)`);
  }

  public async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`DROP INDEX idx_events_user_id`);
    await queryRunner.query(`DROP TABLE events`);
  }
}
```

---

## Query Rules

### QueryBuilder

- Use QueryBuilder for any query with conditions, joins, or ordering
- Always parameterize values (`:paramName`, never string interpolation)
- Always filter by `userId` for user-owned resources
- Always exclude soft-deleted (`deleted_at IS NULL`)

```typescript
// GOOD
qb.where('event.userId = :userId', { userId })
  .andWhere('event.deletedAt IS NULL');

// BAD - SQL injection risk
qb.where(`event.userId = '${userId}'`);
```

### Pagination

- Use cursor-based pagination for infinite scroll (mobile)
- Use offset pagination only for admin/dashboard
- Always set reasonable limits (default 20, max 100)

```typescript
interface PaginatedResult<T> {
  data: T[];
  meta: {
    total: number;
    page: number;
    limit: number;
    hasNext: boolean;
  };
}
```

### Transactions

- Use transactions for multi-table operations
- Always handle rollback on failure
- Keep transactions short (avoid API calls inside transactions)

---

## SQLite Offline Rules

### Schema Mirroring

- Offline SQLite schema mirrors backend PostgreSQL (same table/column names)
- Add `sync_status` column to every offline table: `'synced' | 'pending' | 'conflict'`
- Add `local_updated_at` for conflict detection

### Sync Strategy

1. Read from local SQLite first (offline-first)
2. Queue mutations when offline
3. Sync queue when connectivity restored
4. Server timestamp wins on conflict (unless user explicitly resolves)
5. Track sync status per-record

### Offline Tables

```sql
-- Additional columns for offline tracking
sync_status   TEXT DEFAULT 'synced'   -- 'synced' | 'pending' | 'conflict'
local_updated_at  TEXT                -- ISO timestamp of local change
server_updated_at TEXT                -- ISO timestamp from server
```
