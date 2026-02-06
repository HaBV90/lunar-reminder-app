# Events Table

**Purpose:** Store calendar events (lunar and solar)

---

## Table Definition

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

---

## Default Notification Time Slots

```json
[
  {"hour": 6, "minute": 30},
  {"hour": 11, "minute": 45},
  {"hour": 18, "minute": 30},
  {"hour": 20, "minute": 30}
]
```

---

## Indexes

```sql
CREATE INDEX idx_events_user_id ON events(user_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_events_solar_date ON events(solar_date) WHERE deleted_at IS NULL;
CREATE INDEX idx_events_type ON events(type) WHERE deleted_at IS NULL;
CREATE INDEX idx_events_notify_enabled ON events(notify_enabled) WHERE notify_enabled = TRUE AND deleted_at IS NULL;
CREATE INDEX idx_events_user_solar_date ON events(user_id, solar_date) WHERE deleted_at IS NULL;
```

---

## Relationships

### Parent
- **users**: Each event belongs to one user (FK: user_id)

### Many-to-Many
- **events <-> groups**: One event can belong to multiple groups via `event_groups` junction table

### Children
- **notifications**: One event has many scheduled notifications

---

## Sample Data

```sql
-- Gio (Lunar)
INSERT INTO events (user_id, name, type, calendar_type, lunar_day, lunar_month, solar_date, notify_days_before) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'Gio ong noi', 'gio', 'lunar', 15, 3, '2026-04-18', 3);

-- Sinh nhat (Solar)
INSERT INTO events (user_id, name, type, calendar_type, solar_date, notify_days_before) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'Sinh nhat me', 'sinh_nhat', 'solar', '2026-05-25', 1);

-- Le (Lunar)
INSERT INTO events (user_id, name, type, calendar_type, lunar_day, lunar_month, solar_date) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'Tet Nguyen Dan', 'le', 'lunar', 1, 1, '2026-02-17');
```

---

## Useful Queries

### Find upcoming events

```sql
SELECT e.*,
       (e.solar_date - CURRENT_DATE) AS days_until
FROM events e
WHERE e.deleted_at IS NULL
  AND e.solar_date >= CURRENT_DATE
  AND e.solar_date <= CURRENT_DATE + INTERVAL '30 days'
ORDER BY e.solar_date;
```

### Find events needing notifications

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

## Related Documents

- [Data Model Overview](../index.md)
- [Users Table](./users.md) - Event owner
- [Groups Table](./groups.md) - Event grouping
- [Notifications Table](./notifications.md) - Event notifications
- [Enums](../enums.md) - Event types and calendar types

---

**End of Events Table Documentation**
