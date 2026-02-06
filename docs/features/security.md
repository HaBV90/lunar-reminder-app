# Security Implementation

## Overview

Security measures for authentication, data protection, and API security.

---

## Authentication

### JWT Token Structure

```json
{
  "sub": "user_uuid",
  "email": "user@example.com",
  "iat": 1234567890,
  "exp": 1234571490
}
```

### Token Lifecycle
- **Access token:** 15 minutes expiry
- **Refresh token:** 7 days expiry
- **Storage:** Keychain (iOS) / Keystore (Android)

### JWT Strategy

```typescript
@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(private usersService: UsersService) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      secretOrKey: process.env.JWT_SECRET
    });
  }

  async validate(payload: JwtPayload) {
    const user = await this.usersService.findById(payload.sub);
    if (!user || !user.isActive) {
      throw new UnauthorizedException();
    }
    return user;
  }
}
```

---

## API Security

### Rate Limiting

```typescript
// main.ts
import rateLimit from 'express-rate-limit';

app.use(
  rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // limit per IP
    message: 'Too many requests from this IP'
  })
);
```

### Input Validation

```typescript
// create-event.dto.ts
export class CreateEventDto {
  @IsString()
  @IsNotEmpty()
  @MaxLength(200)
  name: string;

  @IsEnum(EventType)
  type: EventType;

  @IsEnum(CalendarType)
  calendarType: CalendarType;

  @ValidateIf(o => o.calendarType === 'lunar')
  @ValidateNested()
  @Type(() => LunarDateDto)
  lunarDate?: LunarDateDto;

  @IsDateString()
  solarDate: string;
}
```

### SQL Injection Prevention
- Use TypeORM parameterized queries
- Never concatenate user input into queries

```typescript
// GOOD
const events = await this.eventRepo.find({
  where: { userId: userId }
});

// BAD - Never do this
const events = await this.eventRepo.query(
  `SELECT * FROM events WHERE user_id = '${userId}'`
);
```

### XSS Prevention
- Sanitize user input before storage
- Escape output in frontend
- Use React's built-in XSS protection

---

## Data Encryption

### At Rest
- PostgreSQL: Use `pgcrypto` extension for sensitive fields
- Mobile: Use `react-native-encrypted-storage`

```typescript
// Secure storage on mobile
import EncryptedStorage from 'react-native-encrypted-storage';

await EncryptedStorage.setItem('access_token', token);
const token = await EncryptedStorage.getItem('access_token');
```

### In Transit
- HTTPS only (via Cloudflare Tunnel)
- TLS 1.3 minimum

### Password Hashing

```typescript
import * as bcrypt from 'bcrypt';

async hashPassword(password: string): Promise<string> {
  const salt = await bcrypt.genSalt(10);
  return bcrypt.hash(password, salt);
}

async comparePassword(password: string, hash: string): Promise<boolean> {
  return bcrypt.compare(password, hash);
}
```

---

## Security Headers

```typescript
// main.ts
import helmet from 'helmet';

app.use(helmet());
```

### Headers Applied
- `X-Frame-Options: DENY`
- `X-Content-Type-Options: nosniff`
- `X-XSS-Protection: 1; mode=block`
- `Strict-Transport-Security: max-age=31536000`

---

## Authentication Flow

```
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│   Client    │      │   Server    │      │  Database   │
└──────┬──────┘      └──────┬──────┘      └──────┬──────┘
       │                     │                    │
       │ POST /auth/login    │                    │
       │ {email, password}   │                    │
       │────────────────────►│                    │
       │                     │ Find user by email │
       │                     │───────────────────►│
       │                     │◄───────────────────│
       │                     │ Verify password    │
       │                     │ Generate JWT       │
       │◄────────────────────│                    │
       │ {access_token,      │                    │
       │  refresh_token}     │                    │
       │                     │                    │
       │ GET /events         │                    │
       │ Authorization:      │                    │
       │ Bearer <token>      │                    │
       │────────────────────►│                    │
       │                     │ Validate JWT       │
       │                     │ Extract user_id    │
       │                     │───────────────────►│
       │◄────────────────────│                    │
```

---

## Security Checklist

- [ ] HTTPS only
- [ ] JWT with short expiry
- [ ] Refresh token rotation
- [ ] Password hashing (bcrypt)
- [ ] Input validation (class-validator)
- [ ] Rate limiting
- [ ] SQL injection prevention
- [ ] XSS prevention
- [ ] CORS configuration
- [ ] Security headers (helmet)
- [ ] Secure token storage

---

## Related

- [Auth API](../api/auth-endpoints.md)
- [Architecture Overview](../architecture/index.md)
