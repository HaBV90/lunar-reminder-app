# Security Rules

## Authentication

### JWT Strategy

- **Access token:** 15 minutes expiry, signed with RS256 or HS256
- **Refresh token:** 7 days expiry, stored in database (revocable)
- **Token rotation:** Issue new refresh token on each refresh (invalidate old one)
- **Logout:** Invalidate refresh token in database

### Token Storage (Mobile)

- **iOS:** Keychain Services (via `react-native-keychain`)
- **Android:** Android Keystore
- **Never** store tokens in AsyncStorage, localStorage, or plain SharedPreferences
- Clear tokens on logout and app uninstall detection

### Auth Flow

```
Login → Access Token + Refresh Token
API Call → Access Token in Authorization header
Token Expired → Use Refresh Token to get new pair
Refresh Expired → Redirect to login
```

---

## Authorization

### Ownership Check (MANDATORY)

Every service method that accesses user-owned data MUST verify ownership:

```typescript
// REQUIRED on every find/update/delete
async findOne(id: string, userId: string): Promise<Event> {
  const event = await this.repository.findOne({
    where: { id, deletedAt: null },
  });

  if (!event) {
    throw new NotFoundException('Event not found');
  }

  if (event.userId !== userId) {
    throw new ForbiddenException('Access denied');
  }

  return event;
}
```

### Rules

1. Never trust client-provided `userId` — extract from JWT token
2. Filter all queries by authenticated `userId`
3. Use `@CurrentUser()` decorator in controllers
4. Group operations: verify membership before access

---

## Input Validation

### DTO Validation (All Endpoints)

```typescript
// Every DTO MUST have class-validator decorators
export class CreateEventDto {
  @IsString()
  @IsNotEmpty()
  @MaxLength(200)
  name: string;

  @IsEnum(EventType)
  type: EventType;
}
```

### Rules

1. **All DTOs** must use class-validator decorators
2. **Enable** `ValidationPipe` globally with `whitelist: true` and `forbidNonWhitelisted: true`
3. **MaxLength** on all string fields
4. **Sanitize HTML** — strip tags from user input
5. **Validate Vietnamese characters** — allow Unicode in name fields
6. **File uploads** — validate type, size (max 5MB images), use content-type detection

### Global Validation Pipe

```typescript
app.useGlobalPipes(new ValidationPipe({
  whitelist: true,
  forbidNonWhitelisted: true,
  transform: true,
  transformOptions: { enableImplicitConversion: true },
}));
```

---

## Data Protection

### Password Security

- Hash with **bcrypt** (salt rounds: 12)
- Minimum 8 characters, require uppercase + lowercase + number
- Never log or return passwords in any response
- Rate limit login attempts (5 per minute per IP)

### HTTPS

- All API communication over HTTPS only
- Pin SSL certificate in production mobile app
- Reject HTTP connections

### Helmet & Headers

```typescript
app.use(helmet());
// Sets: X-Content-Type-Options, X-Frame-Options, X-XSS-Protection, etc.
```

### CORS

```typescript
app.enableCors({
  origin: ['https://your-domain.com'], // Explicit origins
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
  credentials: true,
});
```

### Secrets Management

- **Never** hardcode secrets in source code
- Use `.env` files locally (in `.gitignore`)
- Use GitHub Secrets / cloud secret manager for CI/CD and production
- **Never** log: passwords, tokens, API keys, user PII
- `.env.example` with placeholder values committed to repo

---

## SQL Injection Prevention

### Parameterized Queries Only

```typescript
// GOOD - parameterized
qb.where('event.userId = :userId', { userId });
repository.findOne({ where: { id, userId } });

// BAD - string interpolation (SQL injection risk)
qb.where(`event.userId = '${userId}'`);
repository.query(`SELECT * FROM events WHERE user_id = '${userId}'`);
```

### Rules

1. **Never** use string interpolation in queries
2. **Always** use TypeORM query parameters or `where` objects
3. **Never** pass raw user input to `repository.query()`
4. Code review must flag any raw SQL string concatenation

---

## Rate Limiting

```typescript
// Apply globally or per-route
@UseGuards(ThrottlerGuard)
@Throttle(5, 60) // 5 requests per 60 seconds
@Post('auth/login')
async login() { ... }
```

- Login: 5 attempts per minute per IP
- Password reset: 3 per hour
- API general: 100 requests per minute per user
- Registration: 3 per hour per IP

---

## OWASP Mobile Top 10 Checklist

| # | Risk | Mitigation |
|---|------|-----------|
| M1 | Improper Platform Usage | Follow iOS/Android security guidelines |
| M2 | Insecure Data Storage | Keychain/Keystore for tokens, no plaintext secrets |
| M3 | Insecure Communication | HTTPS only, certificate pinning |
| M4 | Insecure Authentication | JWT + refresh rotation, session timeout |
| M5 | Insufficient Cryptography | bcrypt for passwords, strong JWT signing |
| M6 | Insecure Authorization | Ownership check on every operation |
| M7 | Client Code Quality | TypeScript strict, linting, code review |
| M8 | Code Tampering | Code signing, integrity checks |
| M9 | Reverse Engineering | ProGuard/R8 (Android), no secrets in bundle |
| M10 | Extraneous Functionality | Remove debug endpoints in production |
