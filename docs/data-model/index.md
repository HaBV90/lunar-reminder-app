# Data Model Overview

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Status:** Approved
**Owner:** Database Architect / Backend Lead

---

## Table of Contents

1. [Overview](#1-overview)
2. [Entity Relationship Diagram](#2-entity-relationship-diagram)
3. [Related Documents](#related-documents)

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

## Related Documents

### Table Definitions
- [users table](./tables/users.md) - User accounts and profiles
- [events table](./tables/events.md) - Calendar events with lunar/solar support
- [groups table](./tables/groups.md) - Event groups and event_groups junction
- [shares table](./tables/shares.md) - Sharing links and shared_events
- [notifications table](./tables/notifications.md) - Notifications, devices, sync_logs

### Data Types and Enums
- [Enums](./enums.md) - Event types, calendar types, notification status, etc.

### Operations
- [Migrations](./migrations.md) - Migration strategy, local database, backup & recovery

### Other Documentation
- [Requirements](../requirements/index.md) - Feature requirements
- [Architecture](../architecture/index.md) - Architecture decisions
- [Glossary](../reference/glossary.md) - Vietnamese terms and definitions

---

**End of Data Model Overview**
