# Notifications, Devices, and Sync Logs Tables

**Purpose:** Manage push notifications, user devices, and data synchronization tracking

---

## notifications Table

**Purpose:** Store scheduled notifications

### Table Definition

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

### Indexes

```sql
CREATE INDEX idx_notifications_scheduled_at ON notifications(scheduled_at) WHERE status = 'pending';
CREATE INDEX idx_notifications_status ON notifications(status);
CREATE INDEX idx_notifications_user_id ON notifications(user_id);
CREATE INDEX idx_notifications_event_id ON notifications(event_id);
```

### Notification Status Values

| Status | Description |
|--------|-------------|
| `pending` | Not sent yet, waiting for scheduled time |
| `sent` | Successfully delivered |
| `failed` | Error occurred during sending |
| `cancelled` | User disabled notifications |

---

## devices Table

**Purpose:** Track user devices for push notifications

### Table Definition

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

### Indexes

```sql
CREATE INDEX idx_devices_user_id ON devices(user_id) WHERE is_active = TRUE;
CREATE INDEX idx_devices_onesignal_player_id ON devices(onesignal_player_id);
CREATE INDEX idx_devices_platform ON devices(platform);
```

### Platform Values

| Platform | Description |
|----------|-------------|
| `ios` | Apple iOS devices |
| `android` | Android devices |

---

## sync_logs Table

**Purpose:** Track data synchronization events

### Table Definition

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

### Indexes

```sql
CREATE INDEX idx_sync_logs_user_id ON sync_logs(user_id);
CREATE INDEX idx_sync_logs_synced_at ON sync_logs(synced_at);
CREATE INDEX idx_sync_logs_device_id ON sync_logs(device_id);
```

### Sync Type Values

| Sync Type | Description |
|-----------|-------------|
| `pull` | Client pulls data from server |
| `push` | Client pushes data to server |

### Sync Status Values

| Status | Description |
|--------|-------------|
| `success` | All items synced successfully |
| `failed` | Sync failed completely |
| `partial` | Some items synced, some failed |

---

## Relationships

### notifications Table

**Parents:**
- **events**: Each notification is for one event (FK: event_id)
- **users**: Each notification targets one user (FK: user_id)

**Cascade Behavior:**
- CASCADE delete: Delete event or user -> delete notifications

### devices Table

**Parent:**
- **users**: Each device belongs to one user (FK: user_id)
- CASCADE delete: Delete user -> delete device records

**Children:**
- **sync_logs**: Device sync history

### sync_logs Table

**Parents:**
- **users**: Each sync log belongs to one user (FK: user_id)
- **devices**: Each sync log optionally references a device (FK: device_id)

**Cascade Behavior:**
- CASCADE delete: Delete user -> delete sync logs
- SET NULL: Delete device -> set device_id to NULL in sync logs

---

## Usage Notes

### Schedule a Notification

```sql
INSERT INTO notifications (event_id, user_id, scheduled_at)
VALUES ('event-uuid', 'user-uuid', '2026-04-15 06:30:00');
```

### Get Pending Notifications to Send

```sql
SELECT n.*, e.name as event_name, u.full_name as user_name
FROM notifications n
JOIN events e ON n.event_id = e.id
JOIN users u ON n.user_id = u.id
WHERE n.status = 'pending'
  AND n.scheduled_at <= NOW()
ORDER BY n.scheduled_at;
```

### Mark Notification as Sent

```sql
UPDATE notifications
SET status = 'sent',
    sent_at = NOW(),
    onesignal_notification_id = 'onesignal-id'
WHERE id = 'notification-uuid';
```

### Register Device

```sql
INSERT INTO devices (user_id, device_token, platform, onesignal_player_id, app_version, os_version)
VALUES ('user-uuid', 'device-token', 'ios', 'player-id', '1.0.0', '17.0');
```

### Log Sync Operation

```sql
INSERT INTO sync_logs (user_id, device_id, sync_type, entity_type, status, items_synced)
VALUES ('user-uuid', 'device-uuid', 'pull', 'events', 'success', 25);
```

---

## Related Documents

- [Data Model Overview](../index.md)
- [Users Table](./users.md) - User owning devices
- [Events Table](./events.md) - Events triggering notifications
- [Enums](../enums.md) - Notification status, platform types
- [Migrations](../migrations.md) - Local database sync tables

---

**End of Notifications Table Documentation**
