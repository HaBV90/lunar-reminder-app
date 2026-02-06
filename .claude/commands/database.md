# Database Skill

Generate database-related code following project conventions.

See rules: `.claude/rules/database.md`

---

## TypeORM Entity Template

```typescript
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  CreateDateColumn,
  UpdateDateColumn,
  DeleteDateColumn,
  ManyToOne,
  JoinColumn,
  Index,
} from 'typeorm';
import { User } from '@modules/users/entities/user.entity';

@Entity('$TABLE_NAME')
@Index('idx_${TABLE_NAME}_user_id', ['userId'])
export class $ENTITY_NAME {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ name: 'user_id' })
  userId: string;

  @ManyToOne(() => User, { onDelete: 'CASCADE' })
  @JoinColumn({ name: 'user_id' })
  user: User;

  // --- Domain columns ---

  @Column({ length: 200 })
  name: string;

  // --- Timestamps ---

  @CreateDateColumn({ name: 'created_at' })
  createdAt: Date;

  @UpdateDateColumn({ name: 'updated_at' })
  updatedAt: Date;

  @DeleteDateColumn({ name: 'deleted_at' })
  deletedAt: Date;
}
```

---

## Lunar Event Entity (Project-Specific)

```typescript
@Entity('events')
@Index('idx_events_user_id', ['userId'])
@Index('idx_events_solar_date', ['solarDate'])
@Index('idx_events_type', ['type'])
export class Event {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ name: 'user_id' })
  userId: string;

  @ManyToOne(() => User, { onDelete: 'CASCADE' })
  @JoinColumn({ name: 'user_id' })
  user: User;

  @Column({ length: 200 })
  name: string;

  @Column({ type: 'varchar', length: 50 })
  type: EventType;

  // Lunar date components
  @Column({ name: 'lunar_day', type: 'int' })
  lunarDay: number;

  @Column({ name: 'lunar_month', type: 'int' })
  lunarMonth: number;

  @Column({ name: 'lunar_year', type: 'int', nullable: true })
  lunarYear: number | null;

  @Column({ name: 'is_leap_month', default: false })
  isLeapMonth: boolean;

  // Computed solar date (next occurrence)
  @Column({ name: 'solar_date', type: 'date', nullable: true })
  solarDate: Date | null;

  @Column({ type: 'varchar', length: 500, nullable: true })
  notes: string | null;

  @Column({ name: 'is_recurring', default: true })
  isRecurring: boolean;

  @CreateDateColumn({ name: 'created_at' })
  createdAt: Date;

  @UpdateDateColumn({ name: 'updated_at' })
  updatedAt: Date;

  @DeleteDateColumn({ name: 'deleted_at' })
  deletedAt: Date;
}
```

---

## Migration Templates

### CreateTable Migration

```typescript
import { MigrationInterface, QueryRunner } from 'typeorm';

export class Create${TableName}Table${TIMESTAMP} implements MigrationInterface {
  public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`
      CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

      CREATE TABLE $table_name (
        id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
        user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
        name VARCHAR(200) NOT NULL,
        created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
        updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
        deleted_at TIMESTAMPTZ
      );

      CREATE INDEX idx_${table_name}_user_id ON $table_name(user_id);
    `);
  }

  public async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`DROP TABLE IF EXISTS $table_name`);
  }
}
```

### AddColumn Migration

```typescript
export class Add${ColumnName}To${TableName}${TIMESTAMP} implements MigrationInterface {
  public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`
      ALTER TABLE $table_name
      ADD COLUMN $column_name VARCHAR(100) DEFAULT NULL
    `);
  }

  public async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`
      ALTER TABLE $table_name
      DROP COLUMN $column_name
    `);
  }
}
```

### CreateIndex Migration

```typescript
export class CreateIndex${IndexName}${TIMESTAMP} implements MigrationInterface {
  public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`
      CREATE INDEX idx_${table_name}_${columns} ON $table_name($columns)
    `);
  }

  public async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`
      DROP INDEX IF EXISTS idx_${table_name}_${columns}
    `);
  }
}
```

---

## Repository Patterns

### CRUD Repository

```typescript
@Injectable()
export class EventsService {
  constructor(
    @InjectRepository(Event)
    private readonly eventRepository: Repository<Event>,
  ) {}

  async findAll(userId: string, query: FindEventsDto): Promise<PaginatedResult<Event>> {
    const qb = this.eventRepository
      .createQueryBuilder('event')
      .where('event.userId = :userId', { userId })
      .andWhere('event.deletedAt IS NULL');

    if (query.type) {
      qb.andWhere('event.type = :type', { type: query.type });
    }

    if (query.search) {
      qb.andWhere('event.name ILIKE :search', { search: `%${query.search}%` });
    }

    const total = await qb.getCount();
    const page = query.page || 1;
    const limit = Math.min(query.limit || 20, 100);

    const data = await qb
      .orderBy('event.solarDate', 'ASC')
      .skip((page - 1) * limit)
      .take(limit)
      .getMany();

    return {
      data,
      meta: { total, page, limit, hasNext: page * limit < total },
    };
  }

  async findOne(id: string, userId: string): Promise<Event> {
    const event = await this.eventRepository.findOne({
      where: { id, userId, deletedAt: null },
    });

    if (!event) {
      throw new NotFoundException('Event not found');
    }

    return event;
  }

  async create(dto: CreateEventDto, userId: string): Promise<Event> {
    const event = this.eventRepository.create({ ...dto, userId });
    return this.eventRepository.save(event);
  }

  async update(id: string, dto: UpdateEventDto, userId: string): Promise<Event> {
    const event = await this.findOne(id, userId);
    Object.assign(event, dto);
    return this.eventRepository.save(event);
  }

  async softDelete(id: string, userId: string): Promise<void> {
    const event = await this.findOne(id, userId);
    await this.eventRepository.softRemove(event);
  }
}
```

### Transaction Pattern

```typescript
async transferEvent(eventId: string, fromUserId: string, toUserId: string): Promise<void> {
  await this.eventRepository.manager.transaction(async (manager) => {
    const event = await manager.findOne(Event, {
      where: { id: eventId, userId: fromUserId, deletedAt: null },
    });

    if (!event) {
      throw new NotFoundException('Event not found');
    }

    event.userId = toUserId;
    await manager.save(event);
  });
}
```

---

## SQLite Offline Schema Patterns

### Local Table Definition

```typescript
// SQLite table creation (via react-native-sqlite-storage or expo-sqlite)
const CREATE_EVENTS_TABLE = `
  CREATE TABLE IF NOT EXISTS events (
    id TEXT PRIMARY KEY,
    user_id TEXT NOT NULL,
    name TEXT NOT NULL,
    type TEXT NOT NULL,
    lunar_day INTEGER NOT NULL,
    lunar_month INTEGER NOT NULL,
    lunar_year INTEGER,
    is_leap_month INTEGER DEFAULT 0,
    solar_date TEXT,
    notes TEXT,
    is_recurring INTEGER DEFAULT 1,
    created_at TEXT NOT NULL,
    updated_at TEXT NOT NULL,
    deleted_at TEXT,
    sync_status TEXT DEFAULT 'synced',
    local_updated_at TEXT,
    server_updated_at TEXT
  )
`;
```

### Sync Queue

```typescript
const CREATE_SYNC_QUEUE_TABLE = `
  CREATE TABLE IF NOT EXISTS sync_queue (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    entity_type TEXT NOT NULL,
    entity_id TEXT NOT NULL,
    action TEXT NOT NULL,
    payload TEXT NOT NULL,
    created_at TEXT NOT NULL,
    retry_count INTEGER DEFAULT 0,
    last_error TEXT
  )
`;
```

---

## Seed Data Patterns

### Vietnamese Holidays

```typescript
export const vietnameseHolidays = [
  { name: 'Tết Nguyên Đán', lunarDay: 1, lunarMonth: 1, type: 'le', isRecurring: true },
  { name: 'Rằm tháng Giêng', lunarDay: 15, lunarMonth: 1, type: 'le', isRecurring: true },
  { name: 'Tết Hàn Thực', lunarDay: 3, lunarMonth: 3, type: 'le', isRecurring: true },
  { name: 'Tết Đoan Ngọ', lunarDay: 5, lunarMonth: 5, type: 'le', isRecurring: true },
  { name: 'Lễ Vu Lan', lunarDay: 15, lunarMonth: 7, type: 'le', isRecurring: true },
  { name: 'Tết Trung Thu', lunarDay: 15, lunarMonth: 8, type: 'le', isRecurring: true },
  { name: 'Tết Ông Táo', lunarDay: 23, lunarMonth: 12, type: 'le', isRecurring: true },
  { name: 'Tất Niên', lunarDay: 30, lunarMonth: 12, type: 'le', isRecurring: true },
];
```

### Test Users

```typescript
export const testUsers = [
  {
    name: 'Nguyễn Văn A',
    email: 'nguyenvana@example.com',
    password: 'TestPassword123!',
  },
  {
    name: 'Trần Thị B',
    email: 'tranthib@example.com',
    password: 'TestPassword456!',
  },
];
```
