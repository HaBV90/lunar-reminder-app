# Events API Endpoints

All endpoints require authentication: `Authorization: Bearer <token>`

---

## GET /events

List user's events with filtering and pagination.

**Query Parameters:**
| Param | Type | Description |
|-------|------|-------------|
| `page` | number | Page number (default: 1) |
| `limit` | number | Items per page (default: 20) |
| `type` | EventType | Filter by event type |
| `group_id` | uuid | Filter by group |
| `from_date` | date | Start date filter |
| `to_date` | date | End date filter |
| `search` | string | Search by name |

**Response (200):**
```json
{
  "success": true,
  "data": {
    "events": [
      {
        "id": "uuid",
        "name": "Giỗ ông nội",
        "type": "gio",
        "calendar_type": "lunar",
        "lunar_date": { "day": 15, "month": 3, "year": null },
        "solar_date": "2026-04-18",
        "notes": "Cúng tại nhà",
        "notify_enabled": true,
        "groups": [
          { "id": "uuid", "name": "Gia đình nội", "color": "#FF5722" }
        ],
        "countdown_days": 75
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 50,
      "total_pages": 3
    }
  }
}
```

---

## GET /events/:id

Get event details by ID.

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Giỗ ông nội",
    "type": "gio",
    "calendar_type": "lunar",
    "lunar_day": 15,
    "lunar_month": 3,
    "lunar_is_leap": false,
    "solar_date": "2026-04-18",
    "notes": "Cúng tại nhà",
    "notify_enabled": true,
    "notify_days_before": 3,
    "groups": [...],
    "countdown_days": 75
  }
}
```

---

## POST /events

Create a new event.

**Request:**
```json
{
  "name": "Giỗ ông nội",
  "type": "gio",
  "calendar_type": "lunar",
  "lunar_date": {
    "day": 15,
    "month": 3,
    "is_leap": false
  },
  "notes": "Cúng tại nhà",
  "notify_enabled": true,
  "notify_days_before": 3,
  "group_ids": ["uuid1", "uuid2"]
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Giỗ ông nội",
    "solar_date": "2026-04-18"
  }
}
```

---

## PUT /events/:id

Update an existing event.

**Request:** Same as POST (partial update allowed)

**Response (200):** Updated event object

---

## DELETE /events/:id

Delete an event (soft delete).

**Response (200):**
```json
{
  "success": true,
  "message": "Event deleted successfully"
}
```

---

## POST /events/bulk

Create multiple events at once.

**Request:**
```json
{
  "events": [
    { "name": "Event 1", ... },
    { "name": "Event 2", ... }
  ]
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "created": 2,
    "failed": 0,
    "events": [...]
  }
}
```

---

## Related

- [API Overview](./index.md)
- [Groups Endpoints](./groups-endpoints.md)
