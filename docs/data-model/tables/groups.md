# Groups and Event Groups Tables

**Purpose:** Organize events into user-defined groups with many-to-many relationships

---

## groups Table

### Table Definition

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

### Indexes

```sql
CREATE INDEX idx_groups_user_id ON groups(user_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_groups_sort_order ON groups(user_id, sort_order) WHERE deleted_at IS NULL;
```

### Sample Data

```sql
INSERT INTO groups (user_id, name, color, icon) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'Gia dinh noi', '#FF5722', 'family'),
('550e8400-e29b-41d4-a716-446655440000', 'Gia dinh ngoai', '#2196F3', 'family'),
('550e8400-e29b-41d4-a716-446655440000', 'Ban be', '#4CAF50', 'people');
```

---

## event_groups Table

**Purpose:** Many-to-many relationship between events and groups

### Table Definition

```sql
CREATE TABLE event_groups (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    event_id UUID NOT NULL REFERENCES events(id) ON DELETE CASCADE,
    group_id UUID NOT NULL REFERENCES groups(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT NOW(),

    UNIQUE(event_id, group_id)
);

COMMENT ON TABLE event_groups IS 'Many-to-many relationship: events <-> groups';
```

### Indexes

```sql
CREATE INDEX idx_event_groups_event_id ON event_groups(event_id);
CREATE INDEX idx_event_groups_group_id ON event_groups(group_id);
```

---

## Relationships

### groups Table

**Parent:**
- **users**: Each group belongs to one user (FK: user_id)
- CASCADE delete: Delete user -> delete all their groups

**Many-to-Many:**
- **groups <-> events**: One group contains multiple events via `event_groups` junction table

### event_groups Table (Junction)

**Parents:**
- **events**: References the event being grouped (FK: event_id)
- **groups**: References the group containing the event (FK: group_id)

**Cascade Behavior:**
- CASCADE delete: Delete event or group -> delete junction record

---

## Usage Notes

### Adding Event to Group

```sql
INSERT INTO event_groups (event_id, group_id)
VALUES ('event-uuid', 'group-uuid');
```

### Getting Events in a Group

```sql
SELECT e.*
FROM events e
JOIN event_groups eg ON e.id = eg.event_id
WHERE eg.group_id = 'group-uuid'
  AND e.deleted_at IS NULL
ORDER BY e.solar_date;
```

### Getting Groups for an Event

```sql
SELECT g.*
FROM groups g
JOIN event_groups eg ON g.id = eg.group_id
WHERE eg.event_id = 'event-uuid'
  AND g.deleted_at IS NULL
ORDER BY g.sort_order;
```

---

## Related Documents

- [Data Model Overview](../index.md)
- [Users Table](./users.md) - Group owner
- [Events Table](./events.md) - Events in groups

---

**End of Groups Table Documentation**
