# Auth Capability Specification

## Overview

Authentication and user management capability for the Lunar Reminder App.

---

## Features

### FR-AUTH-001: Login/Registration (Optional)
- **Priority:** Medium
- Users can use app offline without login
- Login required for cloud sync
- Message: "Đăng nhập để đồng bộ dữ liệu lên cloud"

### Supported Login Methods
- Email + Password
- Phone number + OTP
- Google Sign-in
- Apple Sign-in (iOS only)

### FR-AUTH-002: Profile Management
- View/edit personal info
- Change password
- Delete account (with confirmation)

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/auth/register` | Create new account |
| POST | `/auth/login` | Login with email/password |
| POST | `/auth/login/google` | Google OAuth login |
| POST | `/auth/login/phone` | Phone + OTP login |
| POST | `/auth/refresh` | Refresh access token |
| POST | `/auth/logout` | Logout current session |

---

## Data Model

### users Table
- `id` UUID (PK)
- `email` VARCHAR(255) UNIQUE
- `phone` VARCHAR(20) UNIQUE
- `password_hash` VARCHAR(255)
- `google_id` VARCHAR(255) UNIQUE
- `apple_id` VARCHAR(255) UNIQUE
- `full_name` VARCHAR(100)
- `is_verified` BOOLEAN
- `is_active` BOOLEAN

See [users table](../../../docs/data-model/tables/users.md) for full schema.

---

## Security

- JWT tokens (15 min access, 7 day refresh)
- BCrypt password hashing
- Secure storage (Keychain/Keystore)
- HTTPS only

See [Security](../../../docs/features/security.md) for details.

---

## User Stories

**US-AUTH-1:** As a user, I want to use the app without login so I can start immediately.

**US-AUTH-2:** As a user, I want to login with Google so I can sync across devices.

**US-AUTH-3:** As a user, I want to delete my account so I can remove all my data.

---

## Reference Documentation

- [Architecture Overview](../../../docs/architecture/index.md)
- [Users Table (full schema)](../../../docs/data-model/tables/users.md)
- [Security Details](../../../docs/features/security.md)
- [API Documentation](../../../docs/api/index.md)
- [Backend Setup Guide](../../../docs/guides/BACKEND_SETUP.md)

---

## Related Specs

- [Events Capability](../events/spec.md)
- [Sync Capability](../settings/spec.md)
