# API Error Codes

## HTTP Status Codes

| Code | Error Code | Description |
|------|------------|-------------|
| 400 | `VALIDATION_ERROR` | Invalid input data |
| 401 | `UNAUTHORIZED` | Missing or invalid token |
| 403 | `FORBIDDEN` | No permission for this action |
| 404 | `NOT_FOUND` | Resource not found |
| 409 | `CONFLICT` | Duplicate resource |
| 429 | `RATE_LIMIT` | Too many requests |
| 500 | `SERVER_ERROR` | Internal server error |
| 503 | `SERVICE_UNAVAILABLE` | Service temporarily down |

---

## Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "email",
        "message": "Email is required"
      },
      {
        "field": "password",
        "message": "Password must be at least 8 characters"
      }
    ]
  },
  "timestamp": "2026-02-02T10:30:00Z"
}
```

---

## Common Error Scenarios

### Authentication Errors

| Scenario | Code | Message |
|----------|------|---------|
| Missing token | 401 | Authentication required |
| Expired token | 401 | Token has expired |
| Invalid token | 401 | Invalid authentication token |
| User not found | 401 | User account not found |
| User disabled | 403 | Account is disabled |

### Validation Errors

| Scenario | Code | Message |
|----------|------|---------|
| Missing required field | 400 | [field] is required |
| Invalid format | 400 | Invalid [field] format |
| Value out of range | 400 | [field] must be between X and Y |
| Invalid date | 400 | Invalid lunar date |

### Resource Errors

| Scenario | Code | Message |
|----------|------|---------|
| Event not found | 404 | Event not found |
| Group not found | 404 | Group not found |
| Share expired | 404 | Share link has expired |
| Duplicate email | 409 | Email already registered |

---

## Related

- [API Overview](./index.md)
