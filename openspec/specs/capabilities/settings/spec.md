# Settings Capability Specification

## Overview

Application settings, data management, and sync capabilities.

---

## Features

### FR-SETTING-001: Notification Settings
- Enable/disable all notifications
- Customize time slots (4 slots)
- Customize frequency (times per day)

### FR-SETTING-002: App Settings
- **Language:** Vietnamese / English
- **Theme:** Light / Dark / Auto
- **Color scheme** customization

### FR-SETTING-003: Account Settings
- View account information
- Change password
- Logout
- Delete account (with confirmation)

### FR-SETTING-004: Data Management
- Export data (JSON format)
- Import data (JSON format)
- Clear all data (with confirmation)
- Merge or replace on import

### FR-SETTING-005: About
- App version
- Privacy Policy link
- Terms of Service link
- Contact support

---

## Data Sync

### FR-SYNC-001: Cloud Sync (Logged In)
- **Priority:** High
- Storage: PostgreSQL server
- Two-way sync: Local ↔ Cloud
- Conflict resolution: Last write wins
- Triggers: App open, data change, every 5 min

### FR-SYNC-002: Offline-First
- **Priority:** Critical
- Full functionality offline
- Local storage: SQLite
- Auto-sync when online

### FR-SYNC-003: Multi-Device Sync
- Same account, multiple devices
- Real-time data sync
- Notifications to all devices

### FR-SYNC-004: Export/Import
- Export: JSON with all events
- Import: Validate → import
- Options: Merge existing / Replace all

---

## Sharing Features

### FR-SHARE-001: Share via Link
- **Priority:** High
- Share: single event, group, or all
- Generate deep link
- Send via Zalo, Messenger, SMS

### FR-SHARE-002: Receive Share
- Tap link → open app (or install prompt)
- Preview shared content
- Accept → sync events

### FR-SHARE-003: Shared Event Management
- Badge: "Shared by [Name]"
- Read-only for recipient
- Can remove from own app

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/user/profile` | Get user profile |
| PUT | `/user/profile` | Update profile |
| POST | `/user/change-password` | Change password |
| DELETE | `/user/account` | Delete account |
| POST | `/sync` | Sync data |
| GET | `/sync/status` | Check sync status |
| POST | `/shares` | Create share link |
| GET | `/shares/:code` | Get share preview |
| POST | `/shares/:code/accept` | Accept share |

---

## Data Model

### sync_logs Table
- `id` UUID (PK)
- `user_id` UUID (FK)
- `device_id` UUID (FK)
- `sync_type`: pull, push
- `entity_type`: events, groups, all
- `status`: success, failed, partial
- `items_synced` INTEGER
- `synced_at` TIMESTAMP

### shares Table
- `id` UUID (PK)
- `sender_id` UUID (FK)
- `share_code` VARCHAR(50) UNIQUE
- `share_type`: event, group, all
- `entity_ids` JSON
- `expires_at` TIMESTAMP
- `max_uses`, `current_uses` INTEGER

---

## Sync Strategy

### Delta Sync with Timestamps
1. Each entity has `updated_at`
2. Client tracks `last_sync_time`
3. On sync: Send changes + request server changes
4. Conflict: Last write wins (server timestamp)

### Sync Flow
```
Client                          Server
  │ POST /sync                    │
  │ { last_sync_time, changes }   │
  │ ─────────────────────────────►│
  │                               │
  │     { server_changes,         │
  │       conflicts, sync_time }  │
  │ ◄─────────────────────────────│
  │                               │
  │ Apply changes locally         │
  │ Update last_sync_time         │
```

---

## Widget Support

### FR-WIDGET-001: Home Screen Widget
- **Priority:** Medium
- **Sizes:**
  - Small (2x2): 1 event + countdown
  - Medium (4x2): 2-3 events
  - Large (4x4): Mini calendar + events

### FR-WIDGET-002: Widget Content
- Event name
- Type icon
- Countdown: "Còn X ngày"
- Dual dates

### FR-WIDGET-003: Widget Interaction
- Tap → Open app
- Auto refresh hourly
- Update on app changes

---

## User Stories

**US-SETTINGS-1:** As a user, I want to change language so I can use the app in English.

**US-SETTINGS-2:** As a user, I want to export my data so I have a backup.

**US-SETTINGS-3:** As a user, I want to sync across devices so I can use my phone and tablet.

---

## Reference Documentation

- [Architecture Overview](../../../docs/architecture/index.md)
- [Shares Table (full schema)](../../../docs/data-model/tables/shares.md)
- [Migration Strategy & Sync](../../../docs/data-model/migrations.md)
- [Data Types & Enums](../../../docs/data-model/enums.md) — ShareType, SyncType, SyncStatus
- [UI/UX Design System](../../../docs/ui-ux/index.md)

---

## Related Specs

- [Auth Capability](../auth/spec.md)
- [Events Capability](../events/spec.md)
- [Notifications Capability](../notifications/spec.md)
