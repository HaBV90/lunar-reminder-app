# Migration Strategy, Local Database, and Backup

**Purpose:** Database migration patterns, SQLite local storage, and backup/recovery procedures

---

## 1. Migration Strategy

### 1.1 Migration Files

**Naming Convention:** `YYYYMMDDHHMMSS_description.sql`

**Example Structure:**
```
migrations/
├── 20260202000001_create_users_table.sql
├── 20260202000002_create_events_table.sql
├── 20260202000003_create_groups_table.sql
├── 20260202000004_create_event_groups_table.sql
├── 20260202000005_create_shares_tables.sql
├── 20260202000006_create_notifications_table.sql
├── 20260202000007_create_devices_table.sql
├── 20260202000008_create_sync_logs_table.sql
├── 20260202000009_create_indexes.sql
└── 20260202000010_seed_sample_data.sql
```

### 1.2 Migration Example

**20260202000001_create_users_table.sql:**
```sql
-- UP
CREATE TABLE users (
    -- [Full schema here]
);

CREATE INDEX idx_users_email ON users(email);
-- [Other indexes]

-- DOWN
DROP TABLE IF EXISTS users CASCADE;
```

### 1.3 TypeORM Migrations

**In NestJS with TypeORM:**

```bash
# Generate migration
npm run migration:generate -- -n CreateUsersTable

# Run migrations
npm run migration:run

# Revert last migration
npm run migration:revert
```

---

## 2. Local Database (SQLite)

### 2.1 Schema Differences

**SQLite Adaptations:**

| PostgreSQL | SQLite | Notes |
|------------|--------|-------|
| UUID | TEXT | SQLite doesn't have UUID type |
| `gen_random_uuid()` | N/A | Generate UUID in app code |
| TIMESTAMP | TEXT | ISO 8601 format |
| JSON | TEXT | Serialize/deserialize in app |
| CHECK constraints | N/A | Validate in app |

### 2.2 Local Tables

**Additional Fields for Offline Sync:**

| Field | Type | Description |
|-------|------|-------------|
| `server_id` | TEXT | UUID from server (NULL if not synced) |
| `sync_status` | TEXT | 'synced', 'pending_upload', 'pending_delete' |
| `last_modified` | TEXT | Timestamp for conflict resolution |

**Example: local_events**

```sql
CREATE TABLE local_events (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    server_id TEXT UNIQUE,  -- UUID from server
    user_id TEXT NOT NULL,
    name TEXT NOT NULL,
    type TEXT NOT NULL,
    calendar_type TEXT NOT NULL,
    lunar_day INTEGER,
    lunar_month INTEGER,
    lunar_year INTEGER,
    lunar_is_leap INTEGER DEFAULT 0,
    lunar_leap_option TEXT,
    solar_date TEXT NOT NULL,
    is_recurring INTEGER DEFAULT 1,
    recurrence_end_date TEXT,
    notes TEXT,
    location TEXT,
    notify_enabled INTEGER DEFAULT 1,
    notify_days_before INTEGER DEFAULT 3,
    notify_times_per_day INTEGER DEFAULT 4,
    notify_time_slots TEXT,  -- JSON as TEXT
    color TEXT,
    icon TEXT,
    sync_status TEXT DEFAULT 'pending_upload',
    last_modified TEXT NOT NULL,
    created_at TEXT NOT NULL,
    updated_at TEXT NOT NULL,
    deleted_at TEXT
);
```

### 2.3 Sync Queue Table

```sql
CREATE TABLE sync_queue (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    operation TEXT NOT NULL,  -- 'create', 'update', 'delete'
    entity_type TEXT NOT NULL,  -- 'event', 'group'
    entity_id INTEGER NOT NULL,
    server_id TEXT,
    data TEXT,  -- JSON payload
    timestamp INTEGER NOT NULL,
    retry_count INTEGER DEFAULT 0,
    error_message TEXT
);
```

### 2.4 Sync Queue Operations

| Operation | Description |
|-----------|-------------|
| `create` | New entity to upload to server |
| `update` | Modified entity to sync with server |
| `delete` | Entity deleted locally, delete on server |

---

## 3. Backup and Recovery

### 3.1 Backup Strategy

**Daily Backups:**
- Automated daily PostgreSQL dumps
- Retention: 30 days
- Location: Local directory + cloud storage

**Backup Script:**
```bash
#!/bin/bash
DATE=$(date +%Y%m%d)
pg_dump -U postgres lunar_reminder > backup_$DATE.sql
gzip backup_$DATE.sql
# Upload to cloud
```

### 3.2 Recovery Procedures

**Full Restore:**
```bash
gunzip backup_20260202.sql.gz
psql -U postgres lunar_reminder < backup_20260202.sql
```

**Point-in-Time Recovery:**
- Enable WAL archiving
- Restore from base backup + WAL segments

### 3.3 Data Export

**User Data Export (JSON):**
```sql
SELECT json_build_object(
    'events', (SELECT json_agg(row_to_json(e.*)) FROM events e WHERE e.user_id = $1),
    'groups', (SELECT json_agg(row_to_json(g.*)) FROM groups g WHERE g.user_id = $1)
) AS user_data;
```

---

## 4. SQL Functions

### 4.1 Update Timestamp Trigger

```sql
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Apply to all tables
CREATE TRIGGER update_users_updated_at BEFORE UPDATE ON users
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_events_updated_at BEFORE UPDATE ON events
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
-- [Repeat for other tables]
```

---

## 5. Full-Text Search (Future)

```sql
-- For searching events by name
CREATE INDEX idx_events_name_trgm ON events USING gin (name gin_trgm_ops);

-- Requires extension
CREATE EXTENSION IF NOT EXISTS pg_trgm;
```

---

## 6. Sync Conflict Resolution

### 6.1 Strategy: Last Write Wins

When a conflict occurs during sync:

1. Compare `last_modified` timestamps
2. The record with the newer timestamp wins
3. Log conflicts for audit

### 6.2 Conflict Types

| Conflict Type | Resolution |
|---------------|------------|
| Both modified | Compare timestamps, use newer |
| Server deleted, local modified | Keep local (re-create on server) |
| Local deleted, server modified | Keep server version |
| Both deleted | Delete permanently |

### 6.3 Sync Flow

```
1. Client requests sync
2. Server returns changes since last sync
3. Client compares with local changes
4. Resolve conflicts using last_modified
5. Apply server changes locally
6. Push local changes to server
7. Update sync_status to 'synced'
8. Clear sync_queue for successful items
```

---

## Related Documents

- [Data Model Overview](./index.md)
- [Notifications Table](./tables/notifications.md) - Sync logs table details
- [Architecture](../architecture/index.md) - Architecture decisions

---

**End of Migration Strategy Documentation**
