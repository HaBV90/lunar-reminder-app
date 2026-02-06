# API Documentation

REST API reference for the Lunar Reminder App backend.

---

## Base URL

| Environment | URL |
|-------------|-----|
| Development | `http://localhost:3000/api/v1` |
| Production | `https://your-tunnel.trycloudflare.com/api/v1` |

---

## Authentication

All endpoints except `/auth/*` require JWT authentication.

**Header:**
```
Authorization: Bearer <JWT_TOKEN>
```

---

## Response Format

### Success Response
```json
{
  "success": true,
  "data": { ... },
  "message": "Success",
  "timestamp": "2026-02-02T10:30:00Z"
}
```

### Error Response
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      { "field": "email", "message": "Email is required" }
    ]
  },
  "timestamp": "2026-02-02T10:30:00Z"
}
```

---

## Endpoints by Domain

### Authentication
- [Auth Endpoints](./auth-endpoints.md) - Login, register, refresh tokens

### Core Features
- [Events Endpoints](./events-endpoints.md) - CRUD operations for events
- [Groups Endpoints](./groups-endpoints.md) - Event group management
- [Notifications Endpoints](./notifications-endpoints.md) - Notification settings

### Sharing & Sync
- [Shares Endpoints](./shares-endpoints.md) - Event sharing via links
- [Sync Endpoints](./sync-endpoints.md) - Data synchronization

### Calendar
- [Calendar Export Endpoints](./calendar-export-endpoints.md) - Google Calendar export
- [Lunar Calendar Endpoints](./lunar-calendar-endpoints.md) - Date conversion

### User Management
- [User Endpoints](./user-endpoints.md) - Profile management

### Reference
- [Error Codes](./error-codes.md) - HTTP status codes and error types

---

## Quick Reference

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/auth/register` | POST | Create account |
| `/auth/login` | POST | Login with credentials |
| `/auth/refresh` | POST | Refresh access token |
| `/events` | GET | List user's events |
| `/events` | POST | Create new event |
| `/events/:id` | GET | Get event details |
| `/events/:id` | PUT | Update event |
| `/events/:id` | DELETE | Delete event |
| `/groups` | GET | List user's groups |
| `/groups` | POST | Create new group |
| `/shares` | POST | Create share link |
| `/shares/:code/accept` | POST | Accept shared events |
| `/sync` | POST | Sync data |
| `/lunar/convert` | POST | Convert lunar to solar date |
| `/user/profile` | GET | Get user profile |

---

## Rate Limiting

- **Window:** 15 minutes
- **Max Requests:** 100 per IP
- **Response:** 429 Too Many Requests

---

## Related Documentation

- [Data Model](../data-model/index.md) - Entity definitions
- [Architecture](../architecture/index.md) - System design
- [Security](../features/security.md) - Authentication details

---

**Last Updated:** 2026-02-03
