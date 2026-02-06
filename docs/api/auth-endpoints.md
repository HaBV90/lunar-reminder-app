# Authentication API Endpoints

## POST /auth/register

Create a new user account.

**Request:**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123!",
  "phone": "+84901234567",
  "full_name": "Nguyen Van A"
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "full_name": "Nguyen Van A"
    },
    "access_token": "jwt_token",
    "refresh_token": "refresh_token"
  }
}
```

---

## POST /auth/login

Login with email and password.

**Request:**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123!"
}
```

**Response (200):** Same as register

---

## POST /auth/login/google

Login with Google OAuth.

**Request:**
```json
{
  "google_token": "google_oauth_token"
}
```

**Response (200):** Same as register

---

## POST /auth/login/phone

Login with phone number and OTP.

**Request:**
```json
{
  "phone": "+84901234567",
  "otp": "123456"
}
```

**Response (200):** Same as register

---

## POST /auth/refresh

Refresh access token.

**Request:**
```json
{
  "refresh_token": "refresh_token"
}
```

**Response (200):**
```json
{
  "access_token": "new_jwt_token"
}
```

---

## POST /auth/logout

Logout current session.

**Headers:** `Authorization: Bearer <token>`

**Response (200):**
```json
{
  "success": true,
  "message": "Logged out successfully"
}
```

---

## Related

- [API Overview](./index.md)
- [Error Codes](./error-codes.md)
