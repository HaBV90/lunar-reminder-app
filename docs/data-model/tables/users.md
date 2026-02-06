# Users Table

**Purpose:** Store user account information

---

## Table Definition

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    -- Authentication
    email VARCHAR(255) UNIQUE,
    phone VARCHAR(20) UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    google_id VARCHAR(255) UNIQUE,
    apple_id VARCHAR(255) UNIQUE,

    -- Profile
    full_name VARCHAR(100),
    avatar_url TEXT,

    -- Preferences
    language VARCHAR(5) DEFAULT 'vi',
    timezone VARCHAR(50) DEFAULT 'Asia/Ho_Chi_Minh',

    -- Status
    is_verified BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    last_login_at TIMESTAMP,

    -- Timestamps
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    deleted_at TIMESTAMP
);

COMMENT ON TABLE users IS 'User accounts and profiles';
COMMENT ON COLUMN users.email IS 'Email address for login';
COMMENT ON COLUMN users.phone IS 'Phone number for SMS/OTP login';
COMMENT ON COLUMN users.password_hash IS 'BCrypt hashed password';
COMMENT ON COLUMN users.google_id IS 'Google OAuth user ID';
COMMENT ON COLUMN users.apple_id IS 'Apple Sign In user ID';
COMMENT ON COLUMN users.language IS 'Preferred language: vi, en';
COMMENT ON COLUMN users.timezone IS 'IANA timezone identifier';
```

---

## Constraints

- At least one of `email` or `phone` must be present
- `password_hash` required unless social login
- `email` and `phone` must be unique (case-insensitive)

---

## Indexes

```sql
CREATE INDEX idx_users_email ON users(email) WHERE deleted_at IS NULL;
CREATE INDEX idx_users_phone ON users(phone) WHERE deleted_at IS NULL;
CREATE INDEX idx_users_google_id ON users(google_id) WHERE google_id IS NOT NULL;
CREATE INDEX idx_users_apple_id ON users(apple_id) WHERE apple_id IS NOT NULL;
```

---

## Relationships

### One-to-Many

**users -> events:**
- One user has many events
- CASCADE delete: Delete user -> delete all their events

**users -> groups:**
- One user has many groups
- CASCADE delete: Delete user -> delete all their groups

**users -> devices:**
- One user has many devices
- CASCADE delete: Delete user -> delete device records

---

## Sample Data

```sql
INSERT INTO users (id, email, password_hash, full_name, language) VALUES
('550e8400-e29b-41d4-a716-446655440000', 'minh@example.com', '$2b$10$...', 'Nguyen Van Minh', 'vi'),
('550e8400-e29b-41d4-a716-446655440001', 'lan@example.com', '$2b$10$...', 'Tran Thi Lan', 'vi'),
('550e8400-e29b-41d4-a716-446655440002', 'an@example.com', '$2b$10$...', 'Le Hoang An', 'vi');
```

---

## Related Documents

- [Data Model Overview](../index.md)
- [Events Table](./events.md) - Events owned by users
- [Groups Table](./groups.md) - Groups owned by users
- [Notifications Table](./notifications.md) - Devices and notifications for users
- [Shares Table](./shares.md) - Sharing between users

---

**End of Users Table Documentation**
