# Data Model Specification
# Lunar Reminder App

---

**Document Version:** 1.0  
**Last Updated:** 2026-02-02  
**Status:** ✅ Approved  
**Owner:** Database Architect / Backend Lead

---

## Table of Contents

1. [Overview](#1-overview)
2. [Entity Relationship Diagram](#2-entity-relationship-diagram)
3. [Table Definitions](#3-table-definitions)
4. [Indexes & Constraints](#4-indexes--constraints)
5. [Data Types & Enums](#5-data-types--enums)
6. [Relationships](#6-relationships)
7. [Sample Data](#7-sample-data)
8. [Migration Strategy](#8-migration-strategy)
9. [Local Database (SQLite)](#9-local-database-sqlite)
10. [Backup & Recovery](#10-backup--recovery)

---

## 1. Overview

### 1.1 Database Architecture

**Primary Database:** PostgreSQL 14+  
**Purpose:** Server-side data storage, sync source of truth  
**Location:** Local machine (dev/staging), Cloud (production)

**Local Database:** SQLite  
**Purpose:** Mobile app offline storage  
**Location:** User's device

### 1.2 Design Principles

1. **Normalization:** 3NF (Third Normal Form)
2. **Soft Deletes:** Use `deleted_at` timestamp
3. **Timestamps:** All tables have `created_at`, `updated_at`
4. **UUIDs:** Primary keys for distributed system
5. **Referential Integrity:** Foreign keys with cascade rules

### 1.3 Tables Overview

**Total Tables:** 10

**Core Entities:**
1. `users` - User accounts
2. `events` - Calendar events
3. `groups` - Event groups
4. `event_groups` - Many-to-many relationship

**Sharing & Collaboration:**
5. `shares` - Share links
6. `shared_events` - Tracking shared events

**Notifications:**
7. `notifications` - Scheduled notifications
8. `devices` - User devices for push notifications

**System:**
9. `sync_logs` - Data synchronization tracking
10. `migrations` - Database version control

---

## 2. Entity Relationship Diagram

### 2.1 Complete ERD

```
┌─────────────────────────────────────────────────────────────────┐
│                         users                                   │
│─────────────────────────────────────────────────────────────────│
│ PK │ id UUID                                                    │
│    │ email VARCHAR(255) UNIQUE                                  │
│    │ phone VARCHAR(20) UNIQUE                                   │
│    │ password_hash VARCHAR(255)                                 │
│    │ google_id VARCHAR(255) UNIQUE                              │
│    │ apple_id VARCHAR(255) UNIQUE                               │
│    │ full_name VARCHAR(100)                                     │
│    │ avatar_url TEXT                                            │
│    │ language VARCHAR(5) DEFAULT 'vi'                           │
│    │ timezone VARCHAR(50) DEFAULT 'Asia/Ho_Chi_Minh'            │
│    │ is_verified BOOLEAN DEFAULT FALSE                          │
│    │ is_active BOOLEAN DEFAULT TRUE                             │
│    │ last_login_at TIMESTAMP                                    │
│    │ created_at TIMESTAMP DEFAULT NOW()                         │
│    │ updated_at TIMESTAMP DEFAULT NOW()                         │
│    │ deleted_at TIMESTAMP                                       │
└─────────────────────────────────────────────────────────────────┘
         │ 1
         │
         │ *
┌─────────────────────────────────────────────────────────────────┐
│                        events                                    │
│─────────────────────────────────────────────────────────────────│
│ PK │ id UUID                                                    │
│ FK │ user_id UUID → users(id)                                   │
│    │ name VARCHAR(200) NOT NULL                                 │
│    │ type VARCHAR(50) NOT NULL                                  │
│    │ calendar_type VARCHAR(10) NOT NULL                         │
│    │ -- Lunar date fields                                       │
│    │ lunar_day INTEGER                                          │
│    │ lunar_month INTEGER                                        │
│    │ lunar_year INTEGER                                         │
│    │ lunar_is_leap BOOLEAN DEFAULT FALSE                        │
│    │ lunar_leap_option VARCHAR(10)                              │
│    │ -- Solar date                                              │
│    │ solar_date DATE NOT NULL                                   │
│    │ -- Recurrence                                              │
│    │ is_recurring BOOLEAN DEFAULT TRUE                          │
│    │ recurrence_end_date DATE                                   │
│    │ -- Details                                                 │
│    │ notes TEXT                                                 │
│    │ location VARCHAR(255)                                      │
│    │ -- Notifications                                           │
│    │ notify_enabled BOOLEAN DEFAULT TRUE                        │
│    │ notify_days_before INTEGER DEFAULT 3                       │
│    │ notify_times_per_day INTEGER DEFAULT 4                     │
│    │ notify_time_slots JSON                                     │
│    │ -- Metadata                                                │
│    │ color VARCHAR(20)                                          │
│    │ icon VARCHAR(50)                                           │
│    │ created_at TIMESTAMP DEFAULT NOW()                         │
│    │ updated_at TIMESTAMP DEFAULT NOW()                         │
│    │ deleted_at TIMESTAMP                                       │
└─────────────────────────────────────────────────────────────────┘
         │ *
         │
         │ *
┌─────────────────────────────────────────────────────────────────┐
│                     event_groups                                 │
│─────────────────────────────────────────────────────────────────│
│ PK │ id UUID                                                    │
│ FK │ event_id UUID → events(id)                                 │
│ FK │ group_id UUID → groups(id)                                 │
│    │ created_at TIMESTAMP DEFAULT NOW()                         │
│    │ UNIQUE(event_id, group_id)                                 │
└─────────────────────────────────────────────────────────────────┘
         │ *
         │
         │ 1
┌─────────────────────────────────────────────────────────────────┐
│                         groups                                   │
│─────────────────────────────────────────────────────────────────│
│ PK │ id UUID                                                    │
│ FK │ user_id UUID → users(id)                                   │
│    │ name VARCHAR(100) NOT NULL                                 │
│    │ description TEXT                                           │
│    │ color VARCHAR(20)                                          │
│    │ icon VARCHAR(50)                                           │
│    │ sort_order INTEGER DEFAULT 0                               │
│    │ created_at TIMESTAMP DEFAULT NOW()                         │
│    │ updated_at TIMESTAMP DEFAULT NOW()                         │
│    │ deleted_at TIMESTAMP                                       │
└─────────────────────────────────────────────────────────────────┘


┌─────────────────────────────────────────────────────────────────┐
│                         shares                                   │
│─────────────────────────────────────────────────────────────────│
│ PK │ id UUID                                                    │
│ FK │ sender_id UUID → users(id)                                 │
│    │ share_code VARCHAR(50) UNIQUE NOT NULL                     │
│    │ share_type VARCHAR(20) NOT NULL                            │
│    │ entity_ids JSON                                            │
│    │ expires_at TIMESTAMP                                       │
│    │ max_uses INTEGER                                           │
│    │ current_uses INTEGER DEFAULT 0                             │
│    │ created_at TIMESTAMP DEFAULT NOW()                         │
└─────────────────────────────────────────────────────────────────┘
         │ 1
         │
         │ *
┌─────────────────────────────────────────────────────────────────┐
│                     shared_events                                │
│─────────────────────────────────────────────────────────────────│
│ PK │ id UUID                                                    │
│ FK │ share_id UUID → shares(id)                                 │
│ FK │ recipient_id UUID → users(id)                              │
│ FK │ event_id UUID → events(id)                                 │
│    │ accepted_at TIMESTAMP DEFAULT NOW()                        │
│    │ is_active BOOLEAN DEFAULT TRUE                             │
└─────────────────────────────────────────────────────────────────┘


┌─────────────────────────────────────────────────────────────────┐
│                     notifications                                │
│─────────────────────────────────────────────────────────────────│
│ PK │ id UUID                                                    │
│ FK │ event_id UUID → events(id)                                 │
│ FK │ user_id UUID → users(id)                                   │
│    │ scheduled_at TIMESTAMP NOT NULL                            │
│    │ sent_at TIMESTAMP                                          │
│    │ onesignal_notification_id VARCHAR(100)                     │
│    │ status VARCHAR(20) DEFAULT 'pending'                       │
│    │ error_message TEXT                                         │
│    │ created_at TIMESTAMP DEFAULT NOW()                         │
└─────────────────────────────────────────────────────────────────┘


┌─────────────────────────────────────────────────────────────────┐
│                         devices                                  │
│─────────────────────────────────────────────────────────────────│
│ PK │ id UUID                                                    │
│ FK │ user_id UUID → users(id)                                   │
│    │ device_token VARCHAR(255) NOT NULL                         │
│    │ platform VARCHAR(20) NOT NULL                              │
│    │ onesignal_player_id VARCHAR(100) UNIQUE                    │
│    │ app_version VARCHAR(20)                                    │
│    │ os_version VARCHAR(20)                                     │
│    │ is_active BOOLEAN DEFAULT TRUE                             │
│    │ last_active_at TIMESTAMP DEFAULT NOW()                     │
│    │ created_at TIMESTAMP DEFAULT NOW()                         │
└─────────────────────────────────────────────────────────────────┘


┌─────────────────────────────────────────────────────────────────┐
│                       sync_logs                                  │
│─────────────────────────────────────────────────────────────────│
│ PK │ id UUID                                                    │
│ FK │ user_id UUID → users(id)                                   │
│ FK │ device_id UUID → devices(id)                               │
│    │ sync_type VARCHAR(20) NOT NULL                             │
│    │ entity_type VARCHAR(50)                                    │
│    │ status VARCHAR(20) DEFAULT 'success'                       │
│    │ items_synced INTEGER DEFAULT 0                             │
│    │ error_message TEXT                                         │
│    │ synced_at TIMESTAMP DEFAULT NOW()                          │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3. Table Definitions

### 3.1 users

**Purpose:** Store user account information

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- Authentication
    email VARCHAR(255) UNIQUE,
    phone VARCHAR(20) UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    google_id VARCHAR(255) UNIQUE,
    apple_id VARCHAR(255) UNIQUE,
    
    -- Profile
    full_name VARCHAR(100),
    avatar_url TEXT,
    
    -- Preferences
    language VARCHAR(5) DEFAULT 'vi',
    timezone VARCHAR(50) DEFAULT 'Asia/Ho_Chi_Minh',
    
    -- Status
    is_verified BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    last_login_at TIMESTAMP,
    
    -- Timestamps
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    deleted_at TIMESTAMP
);

COMMENT ON TABLE users IS 'User accounts and profiles';
COMMENT ON COLUMN users.email IS 'Email address for login';
COMMENT ON COLUMN users.phone IS 'Phone number for SMS/OTP login';
COMMENT ON COLUMN users.password_hash IS 'BCrypt hashed password';
COMMENT ON COLUMN users.google_id IS 'Google OAuth user ID';
COMMENT ON COLUMN users.apple_id IS 'Apple Sign In user ID';
COMMENT ON COLUMN users.language IS 'Preferred language: vi, en';
COMMENT ON COLUMN users.timezone IS 'IANA timezone identifier';
```

**Constraints:**
- At least one of `email` or `phone` must be present
- `password_hash` required unless social login
- `email` and `phone` must be unique (case-insensitive)

### 3.2 events

**Purpose:** Store calendar events (lunar and solar)

```sql
CREATE TABLE events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    
    -- Basic Info
    name VARCHAR(200) NOT NULL,
    type VARCHAR(50) NOT NULL CHECK (type IN ('gio', 'sinh_nhat', 'le', 'ngay_cuoi', 'ky_niem', 'khac')),
    calendar_type VARCHAR(10) NOT NULL CHECK (calendar_type IN ('lunar', 'solar')),
    
    -- Lunar Date (if applicable)
    lunar_day INTEGER CHECK (lunar_day BETWEEN 1 AND 30),
    lunar_month INTEGER CHECK (lunar_month BETWEEN 1 AND 12),
    lunar_year INTEGER,
    lunar_is_leap BOOLEAN DEFAULT FALSE,
    lunar_leap_option VARCHAR(10) CHECK (lunar_leap_option IN ('first', 'second', 'both')),
    
    -- Solar Date (always present, calculated if lunar)
    solar_date DATE NOT NULL,
    
    -- Recurrence
    is_recurring BOOLEAN DEFAULT TRUE,
    recurrence_end_date DATE,
    
    -- Details
    notes TEXT,
    location VARCHAR(255),
    
    -- Notification Settings
    notify_enabled BOOLEAN DEFAULT TRUE,
    notify_days_before INTEGER DEFAULT 3 CHECK (notify_days_before >= 0 AND notify_days_before <= 30),
    notify_times_per_day INTEGER DEFAULT 4 CHECK (notify_times_per_day >= 1 AND notify_times_per_day <= 10),
    notify_time_slots JSON,
    
    -- Metadata
    color VARCHAR(20),
    icon VARCHAR(50),
    
    -- Timestamps
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    deleted_at TIMESTAMP,
    
    -- Constraints
    CONSTRAINT lunar_date_required CHECK (
        (calendar_type = 'solar') OR 
        (calendar_type = 'lunar' AND lunar_day IS NOT NULL AND lunar_month IS NOT NULL)
    )
);

COMMENT ON TABLE events IS 'Calendar events with lunar/solar date support';
COMMENT ON COLUMN events.type IS 'Event type: gio (death anniversary), sinh_nhat (birthday), le (festival), ngay_cuoi (wedding), ky_niem (anniversary), khac (other)';
COMMENT ON COLUMN events.calendar_type IS 'Calendar system: lunar or solar';
COMMENT ON COLUMN events.lunar_is_leap IS 'Whether this event is in a leap month';
COMMENT ON COLUMN events.lunar_leap_option IS 'For leap month: which occurrence (first, second, both)';
COMMENT ON COLUMN events.notify_time_slots IS 'JSON array of {hour, minute} objects, e.g. [{"hour": 6, "minute": 30}]';
```

**Default Notification Time Slots:**
```json
[
  {"hour": 6, "minute": 30},
  {"hour": 11, "minute": 45},
  {"hour": 18, "minute": 30},
  {"hour": 20, "minute": 30}
]
```

### 3.3 groups

**Purpose:** Organize events into user-defined groups

```sql
CREATE TABLE groups (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    
    -- Group Info
    name VARCHAR(100) NOT NULL,
    description TEXT,
    
    -- Display
    color VARCHAR(20),
    icon VARCHAR(50),
    sort_order INTEGER DEFAULT 0,
    
    -- Timestamps
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    deleted_at TIMESTAMP
);

COMMENT ON TABLE groups IS 'User-defined event groups';
COMMENT ON COLUMN groups.color IS 'Hex color code, e.g. #FF5722';
COMMENT ON COLUMN groups.icon IS 'Icon name from icon library';
COMMENT ON COLUMN groups.sort_order IS 'Display order, lower number first';
```

### 3.4 event_groups

**Purpose:** Many-to-many relationship between events and groups

```sql
CREATE TABLE event_groups (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    event_id UUID NOT NULL REFERENCES events(id) ON DELETE CASCADE,
    group_id UUID NOT NULL REFERENCES groups(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT NOW(),
    
    UNIQUE(event_id, group_id)
);

COMMENT ON TABLE event_groups IS 'Many-to-many relationship: events ↔ groups';
```

### 3.5 shares

**Purpose:** Manage event sharing via links

```sql
CREATE TABLE shares (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    sender_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    
    -- Share Link
    share_code VARCHAR(50) UNIQUE NOT NULL,
    share_type VARCHAR(20) NOT NULL CHECK (share_type IN ('event', 'group', 'all')),
    entity_ids JSON,  -- Array of event/group UUIDs
    
    -- Expiration & Limits
    expires_at TIMESTAMP,
    max_uses INTEGER,
    current_uses INTEGER DEFAULT 0,
    
    created_at TIMESTAMP DEFAULT NOW()
);

COMMENT ON TABLE shares IS 'Event sharing links';
COMMENT ON COLUMN shares.share_code IS 'Unique code for share URL, e.g. ABC123XYZ';
COMMENT ON COLUMN shares.share_type IS 'What is being shared: event, group, or all events';
COMMENT ON COLUMN shares.entity_ids IS 'JSON array of UUIDs being shared';
COMMENT ON COLUMN shares.max_uses IS 'Maximum times link can be used (NULL = unlimited)';
```

### 3.6 shared_events

**Purpose:** Track which events were shared with whom

```sql
CREATE TABLE shared_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    share_id UUID NOT NULL REFERENCES shares(id) ON DELETE CASCADE,
    recipient_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    event_id UUID NOT NULL REFERENCES events(id) ON DELETE CASCADE,
    
    accepted_at TIMESTAMP DEFAULT NOW(),
    is_active BOOLEAN DEFAULT TRUE
);

COMMENT ON TABLE shared_events IS 'Track shared events and recipients';
COMMENT ON COLUMN shared_events.is_active IS 'Whether recipient still has this event';
```

### 3.7 notifications

**Purpose:** Store scheduled notifications

```sql
CREATE TABLE notifications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    event_id UUID NOT NULL REFERENCES events(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    
    -- Scheduling
    scheduled_at TIMESTAMP NOT NULL,
    sent_at TIMESTAMP,
    
    -- OneSignal
    onesignal_notification_id VARCHAR(100),
    
    -- Status
    status VARCHAR(20) DEFAULT 'pending' CHECK (status IN ('pending', 'sent', 'failed', 'cancelled')),
    error_message TEXT,
    
    created_at TIMESTAMP DEFAULT NOW()
);

COMMENT ON TABLE notifications IS 'Scheduled push notifications';
COMMENT ON COLUMN notifications.status IS 'pending = not sent yet, sent = delivered, failed = error, cancelled = user disabled';
```

### 3.8 devices

**Purpose:** Track user devices for push notifications

```sql
CREATE TABLE devices (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    
    -- Device Info
    device_token VARCHAR(255) NOT NULL,
    platform VARCHAR(20) NOT NULL CHECK (platform IN ('ios', 'android')),
    onesignal_player_id VARCHAR(100) UNIQUE,
    
    -- App Info
    app_version VARCHAR(20),
    os_version VARCHAR(20),
    
    -- Status
    is_active BOOLEAN DEFAULT TRUE,
    last_active_at TIMESTAMP DEFAULT NOW(),
    
    created_at TIMESTAMP DEFAULT NOW()
);

COMMENT ON TABLE devices IS 'User devices for push notifications';
COMMENT ON COLUMN devices.device_token IS 'FCM/APNS device token';
COMMENT ON COLUMN devices.onesignal_player_id IS 'OneSignal player ID';
```

### 3.9 sync_logs

**Purpose:** Track data synchronization events

```sql
CREATE TABLE sync_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    device_id UUID REFERENCES devices(id) ON DELETE SET NULL,
    
    -- Sync Details
    sync_type VARCHAR(20) NOT NULL CHECK (sync_type IN ('pull', 'push')),
    entity_type VARCHAR(50),  -- 'events', 'groups', 'all'
    status VARCHAR(20) DEFAULT 'success' CHECK (status IN ('success', 'failed', 'partial')),
    
    -- Stats
    items_synced INTEGER DEFAULT 0,
    error_message TEXT,
    
    synced_at TIMESTAMP DEFAULT NOW()
);

COMMENT ON TABLE sync_logs IS 'Data synchronization audit log';
COMMENT ON COLUMN sync_logs.sync_type IS 'pull = client pulls from server, push = client pushes to server';
```

---

## 4. Indexes & Constraints

### 4.1 Primary Indexes

```sql
-- users
CREATE INDEX idx_users_email ON users(email) WHERE deleted_at IS NULL;
CREATE INDEX idx_users_phone ON users(phone) WHERE deleted_at IS NULL;
CREATE INDEX idx_users_google_id ON users(google_id) WHERE google_id IS NOT NULL;
CREATE INDEX idx_users_apple_id ON users(apple_id) WHERE apple_id IS NOT NULL;

-- events
CREATE INDEX idx_events_user_id ON events(user_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_events_solar_date ON events(solar_date) WHERE deleted_at IS NULL;
CREATE INDEX idx_events_type ON events(type) WHERE deleted_at IS NULL;
CREATE INDEX idx_events_notify_enabled ON events(notify_enabled) WHERE notify_enabled = TRUE AND deleted_at IS NULL;
CREATE INDEX idx_events_user_solar_date ON events(user_id, solar_date) WHERE deleted_at IS NULL;

-- groups
CREATE INDEX idx_groups_user_id ON groups(user_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_groups_sort_order ON groups(user_id, sort_order) WHERE deleted_at IS NULL;

-- event_groups
CREATE INDEX idx_event_groups_event_id ON event_groups(event_id);
CREATE INDEX idx_event_groups_group_id ON event_groups(group_id);

-- shares
CREATE INDEX idx_shares_share_code ON shares(share_code);
CREATE INDEX idx_shares_sender_id ON shares(sender_id);
CREATE INDEX idx_shares_expires_at ON shares(expires_at) WHERE expires_at IS NOT NULL;

-- shared_events
CREATE INDEX idx_shared_events_recipient_id ON shared_events(recipient_id);
CREATE INDEX idx_shared_events_event_id ON shared_events(event_id);
CREATE INDEX idx_shared_events_share_id ON shared_events(share_id);

-- notifications
CREATE INDEX idx_notifications_scheduled_at ON notifications(scheduled_at) WHERE status = 'pending';
CREATE INDEX idx_notifications_status ON notifications(status);
CREATE INDEX idx_notifications_user_id ON notifications(user_id);
CREATE INDEX idx_notifications_event_id ON notifications(event_id);

-- devices
CREATE INDEX idx_devices_user_id ON devices(user_id) WHERE is_active = TRUE;
CREATE INDEX idx_devices_onesignal_player_id ON devices(onesignal_player_id);
CREATE INDEX idx_devices_platform ON devices(platform);

-- sync_logs
CREATE INDEX idx_sync_logs_user_id ON sync_logs(user_id);
CREATE INDEX idx_sync_logs_synced_at ON sync_logs(synced_at);
CREATE INDEX idx_sync_logs_device_id ON sync_logs(device_id);
```

### 4.2 Full-Text Search (Future)

```sql
-- For searching events by name
CREATE INDEX idx_events_name_trgm ON events USING gin (name gin_trgm_ops);

-- Requires extension
CREATE EXTENSION IF NOT EXISTS pg_trgm;
```

---

## 5. Data Types & Enums

### 5.1 Event Types

```typescript
enum EventType {
  GIO = 'gio',                // Giỗ/Kỵ (Death anniversary)
  SINH_NHAT = 'sinh_nhat',    // Sinh nhật (Birthday)
  LE = 'le',                  // Lễ hội (Festival)
  NGAY_CUOI = 'ngay_cuoi',    // Ngày cưới (Wedding anniversary)
  KY_NIEM = 'ky_niem',        // Kỷ niệm (Other anniversary)
  KHAC = 'khac'               // Khác (Other)
}
```

### 5.2 Calendar Types

```typescript
enum CalendarType {
  LUNAR = 'lunar',  // Lịch âm
  SOLAR = 'solar'   // Lịch dương
}
```

### 5.3 Notification Status

```typescript
enum NotificationStatus {
  PENDING = 'pending',
  SENT = 'sent',
  FAILED = 'failed',
  CANCELLED = 'cancelled'
}
```

### 5.4 Share Types

```typescript
enum ShareType {
  EVENT = 'event',  // Single event
  GROUP = 'group',  // Event group
  ALL = 'all'       // All user's events
}
```

### 5.5 Platform Types

```typescript
enum Platform {
  IOS = 'ios',
  ANDROID = 'android'
}
```

---

## 6. Relationships

### 6.1 One-to-Many

**users → events:**
- One user has many events
- CASCADE delete: Delete user → delete all their events

**users → groups:**
- One user has many groups
- CASCADE delete: Delete user → delete all their groups

**users → devices:**
- One user has many devices
- CASCADE delete: Delete user → delete device records

**events → notifications:**
- One event has many notifications
- CASCADE delete: Delete event → delete notifications

### 6.2 Many-to-Many

**events ↔ groups:**
- One event can belong to multiple groups
- One group contains multiple events
- Junction table: `event_groups`
- CASCADE delete: Delete event or group → delete junction record

### 6.3 Complex Relationships

**Sharing:**
```
users (sender) → shares → shared_events → users (recipient)
                      ↓
                   events
```

- One user can create many shares
- One share can be accepted by many recipients
- One recipient can accept events from many shares

---

## 7. Sample Data

### 7.1 Sample Users

```sql
INSERT INTO users (id, email, password_hash, full_name, language) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'minh@example.com', '$2b$10$...', 'Nguyen Van Minh', 'vi'),
('550e8400-e29b-41d4-a716-446655440001', 'lan@example.com', '$2b$10$...', 'Tran Thi Lan', 'vi'),
('550e8400-e29b-41d4-a716-446655440002', 'an@example.com', '$2b$10$...', 'Le Hoang An', 'vi');
```

### 7.2 Sample Events

```sql
-- Giỗ (Lunar)
INSERT INTO events (user_id, name, type, calendar_type, lunar_day, lunar_month, solar_date, notify_days_before) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'Giỗ ông nội', 'gio', 'lunar', 15, 3, '2026-04-18', 3);

-- Sinh nhật (Solar)
INSERT INTO events (user_id, name, type, calendar_type, solar_date, notify_days_before) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'Sinh nhật mẹ', 'sinh_nhat', 'solar', '2026-05-25', 1);

-- Lễ (Lunar)
INSERT INTO events (user_id, name, type, calendar_type, lunar_day, lunar_month, solar_date) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'Tết Nguyên Đán', 'le', 'lunar', 1, 1, '2026-02-17');
```

### 7.3 Sample Groups

```sql
INSERT INTO groups (user_id, name, color, icon) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'Gia đình nội', '#FF5722', 'family'),
('550e8400-e29b-41d4-a716-446655440000', 'Gia đình ngoại', '#2196F3', 'family'),
('550e8400-e29b-41d4-a716-446655440000', 'Bạn bè', '#4CAF50', 'people');
```

---

## 8. Migration Strategy

### 8.1 Migration Files

**Naming Convention:** `YYYYMMDDHHMMSS_description.sql`

**Example:**
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

### 8.2 Migration Example

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

### 8.3 TypeORM Migrations

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

## 9. Local Database (SQLite)

### 9.1 Schema Differences

**SQLite Adaptations:**
- UUID → TEXT (SQLite doesn't have UUID type)
- `gen_random_uuid()` → Generate UUID in app code
- TIMESTAMP → TEXT (ISO 8601 format)
- JSON → TEXT (serialize/deserialize in app)
- No CHECK constraints (validate in app)

### 9.2 Local Tables

**Additional Fields:**
- `server_id`: UUID from server (NULL if not synced)
- `sync_status`: 'synced', 'pending_upload', 'pending_delete'
- `last_modified`: Timestamp for conflict resolution

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

### 9.3 Sync Queue Table

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

---

## 10. Backup & Recovery

### 10.1 Backup Strategy

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

### 10.2 Recovery Procedures

**Full Restore:**
```bash
gunzip backup_20260202.sql.gz
psql -U postgres lunar_reminder < backup_20260202.sql
```

**Point-in-Time Recovery:**
- Enable WAL archiving
- Restore from base backup + WAL segments

### 10.3 Data Export

**User Data Export (JSON):**
```sql
SELECT json_build_object(
    'events', (SELECT json_agg(row_to_json(e.*)) FROM events e WHERE e.user_id = $1),
    'groups', (SELECT json_agg(row_to_json(g.*)) FROM groups g WHERE g.user_id = $1)
) AS user_data;
```

---

## Appendix

### A. SQL Functions

**Update Timestamp Trigger:**
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

### B. Useful Queries

**Find upcoming events:**
```sql
SELECT e.*, 
       (e.solar_date - CURRENT_DATE) AS days_until
FROM events e
WHERE e.deleted_at IS NULL
  AND e.solar_date >= CURRENT_DATE
  AND e.solar_date <= CURRENT_DATE + INTERVAL '30 days'
ORDER BY e.solar_date;
```

**Find events needing notifications:**
```sql
SELECT e.*
FROM events e
WHERE e.notify_enabled = TRUE
  AND e.deleted_at IS NULL
  AND e.solar_date BETWEEN CURRENT_DATE AND CURRENT_DATE + INTERVAL '7 days'
  AND NOT EXISTS (
      SELECT 1 FROM notifications n 
      WHERE n.event_id = e.id 
        AND n.scheduled_at >= CURRENT_TIMESTAMP 
        AND n.status = 'pending'
  );
```

---

**End of Data Model Specification**

**Next Document:** API Specification (OpenAPI)

