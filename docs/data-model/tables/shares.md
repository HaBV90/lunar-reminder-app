# Shares and Shared Events Tables

**Purpose:** Manage event sharing via links and track shared events between users

---

## shares Table

**Purpose:** Manage event sharing via links

### Table Definition

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

### Indexes

```sql
CREATE INDEX idx_shares_share_code ON shares(share_code);
CREATE INDEX idx_shares_sender_id ON shares(sender_id);
CREATE INDEX idx_shares_expires_at ON shares(expires_at) WHERE expires_at IS NOT NULL;
```

---

## shared_events Table

**Purpose:** Track which events were shared with whom

### Table Definition

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

### Indexes

```sql
CREATE INDEX idx_shared_events_recipient_id ON shared_events(recipient_id);
CREATE INDEX idx_shared_events_event_id ON shared_events(event_id);
CREATE INDEX idx_shared_events_share_id ON shared_events(share_id);
```

---

## Relationships

### Complex Sharing Flow

```
users (sender) -> shares -> shared_events -> users (recipient)
                        |
                     events
```

- One user can create many shares
- One share can be accepted by many recipients
- One recipient can accept events from many shares

### shares Table

**Parent:**
- **users**: Each share is created by one user (FK: sender_id)
- CASCADE delete: Delete sender -> delete all their shares

**Children:**
- **shared_events**: Each share can have many recipients

### shared_events Table

**Parents:**
- **shares**: References the share link (FK: share_id)
- **users**: References the recipient (FK: recipient_id)
- **events**: References the shared event (FK: event_id)

**Cascade Behavior:**
- CASCADE delete: Delete share, recipient, or event -> delete shared_event record

---

## Share Types

| Type | Description |
|------|-------------|
| `event` | Single event |
| `group` | All events in a group |
| `all` | All user's events |

See [Enums](../enums.md) for TypeScript definitions.

---

## Usage Notes

### Creating a Share Link

```sql
INSERT INTO shares (sender_id, share_code, share_type, entity_ids, expires_at, max_uses)
VALUES (
    'user-uuid',
    'ABC123XYZ',
    'event',
    '["event-uuid-1", "event-uuid-2"]',
    NOW() + INTERVAL '7 days',
    5
);
```

### Accepting a Share

```sql
-- Increment usage count
UPDATE shares
SET current_uses = current_uses + 1
WHERE share_code = 'ABC123XYZ';

-- Create shared_event records for each event
INSERT INTO shared_events (share_id, recipient_id, event_id)
VALUES
    ('share-uuid', 'recipient-uuid', 'event-uuid-1'),
    ('share-uuid', 'recipient-uuid', 'event-uuid-2');
```

### Getting Shared Events for a User

```sql
SELECT e.*
FROM events e
JOIN shared_events se ON e.id = se.event_id
WHERE se.recipient_id = 'user-uuid'
  AND se.is_active = TRUE
  AND e.deleted_at IS NULL
ORDER BY e.solar_date;
```

### Checking Share Validity

```sql
SELECT * FROM shares
WHERE share_code = 'ABC123XYZ'
  AND (expires_at IS NULL OR expires_at > NOW())
  AND (max_uses IS NULL OR current_uses < max_uses);
```

---

## Related Documents

- [Data Model Overview](../index.md)
- [Users Table](./users.md) - Sender and recipient users
- [Events Table](./events.md) - Shared events
- [Enums](../enums.md) - Share types

---

**End of Shares Table Documentation**
