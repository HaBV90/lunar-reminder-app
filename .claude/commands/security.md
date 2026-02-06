# Security Skill

Generate security-related code following project conventions.

See rules: `.claude/rules/security.md`

---

## JWT Auth Pattern

### JWT Strategy

```typescript
// backend/src/common/auth/strategies/jwt.strategy.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';
import { ConfigService } from '@nestjs/config';

interface JwtPayload {
  sub: string;
  email: string;
  iat: number;
  exp: number;
}

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(private configService: ConfigService) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: configService.get<string>('JWT_SECRET'),
    });
  }

  async validate(payload: JwtPayload): Promise<{ id: string; email: string }> {
    if (!payload.sub) {
      throw new UnauthorizedException('Invalid token payload');
    }
    return { id: payload.sub, email: payload.email };
  }
}
```

### JWT Auth Guard

```typescript
// backend/src/common/guards/jwt-auth.guard.ts
import { Injectable, ExecutionContext, UnauthorizedException } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {
  handleRequest<TUser>(err: Error | null, user: TUser | false): TUser {
    if (err || !user) {
      throw new UnauthorizedException('Invalid or expired token');
    }
    return user;
  }
}
```

### CurrentUser Decorator

```typescript
// backend/src/common/decorators/current-user.decorator.ts
import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const CurrentUser = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    return request.user;
  },
);
```

### Token Service (with Refresh Rotation)

```typescript
// backend/src/modules/auth/auth.service.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';
import { ConfigService } from '@nestjs/config';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import * as bcrypt from 'bcrypt';
import { RefreshToken } from './entities/refresh-token.entity';
import { User } from '@modules/users/entities/user.entity';

interface TokenPair {
  accessToken: string;
  refreshToken: string;
}

@Injectable()
export class AuthService {
  constructor(
    private jwtService: JwtService,
    private configService: ConfigService,
    @InjectRepository(User)
    private userRepository: Repository<User>,
    @InjectRepository(RefreshToken)
    private refreshTokenRepository: Repository<RefreshToken>,
  ) {}

  async login(email: string, password: string): Promise<TokenPair> {
    const user = await this.userRepository.findOne({ where: { email } });

    if (!user || !(await bcrypt.compare(password, user.passwordHash))) {
      throw new UnauthorizedException('Invalid email or password');
    }

    return this.generateTokenPair(user);
  }

  async refreshTokens(refreshTokenValue: string): Promise<TokenPair> {
    const storedToken = await this.refreshTokenRepository.findOne({
      where: { token: refreshTokenValue, revokedAt: null },
      relations: ['user'],
    });

    if (!storedToken || storedToken.expiresAt < new Date()) {
      throw new UnauthorizedException('Invalid or expired refresh token');
    }

    // Rotate: revoke old token
    storedToken.revokedAt = new Date();
    await this.refreshTokenRepository.save(storedToken);

    // Issue new pair
    return this.generateTokenPair(storedToken.user);
  }

  async logout(userId: string): Promise<void> {
    await this.refreshTokenRepository.update(
      { userId, revokedAt: null },
      { revokedAt: new Date() },
    );
  }

  private async generateTokenPair(user: User): Promise<TokenPair> {
    const payload = { sub: user.id, email: user.email };

    const accessToken = this.jwtService.sign(payload, {
      secret: this.configService.get('JWT_SECRET'),
      expiresIn: this.configService.get('JWT_ACCESS_EXPIRY', '15m'),
    });

    const refreshToken = this.jwtService.sign(payload, {
      secret: this.configService.get('JWT_REFRESH_SECRET'),
      expiresIn: this.configService.get('JWT_REFRESH_EXPIRY', '7d'),
    });

    // Store refresh token in DB
    const tokenEntity = this.refreshTokenRepository.create({
      token: refreshToken,
      userId: user.id,
      expiresAt: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000),
    });
    await this.refreshTokenRepository.save(tokenEntity);

    return { accessToken, refreshToken };
  }
}
```

---

## Password Security

### Hashing

```typescript
import * as bcrypt from 'bcrypt';

const SALT_ROUNDS = 12;

export async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, SALT_ROUNDS);
}

export async function comparePassword(
  password: string,
  hash: string,
): Promise<boolean> {
  return bcrypt.compare(password, hash);
}
```

### Password Strength Validation

```typescript
import { registerDecorator, ValidationOptions, ValidationArguments } from 'class-validator';

export function IsStrongPassword(validationOptions?: ValidationOptions) {
  return function (object: object, propertyName: string): void {
    registerDecorator({
      name: 'isStrongPassword',
      target: object.constructor,
      propertyName,
      options: {
        message: 'Password must be at least 8 characters with uppercase, lowercase, and number',
        ...validationOptions,
      },
      validator: {
        validate(value: string): boolean {
          if (!value || value.length < 8) return false;
          if (!/[A-Z]/.test(value)) return false;
          if (!/[a-z]/.test(value)) return false;
          if (!/[0-9]/.test(value)) return false;
          return true;
        },
      },
    });
  };
}

// Usage in DTO:
export class RegisterDto {
  @IsEmail()
  email: string;

  @IsStrongPassword()
  password: string;
}
```

### Rate Limiting Login

```typescript
// In auth.controller.ts
import { Throttle } from '@nestjs/throttler';

@Throttle({ default: { limit: 5, ttl: 60000 } })
@Post('login')
async login(@Body() dto: LoginDto): Promise<TokenPair> {
  return this.authService.login(dto.email, dto.password);
}
```

---

## Ownership Authorization Pattern

### Reusable Ownership Guard

```typescript
// backend/src/common/guards/ownership.guard.ts
import {
  Injectable,
  CanActivate,
  ExecutionContext,
  ForbiddenException,
  NotFoundException,
} from '@nestjs/common';
import { Reflector } from '@nestjs/core';
import { DataSource, ObjectType } from 'typeorm';

export const OWNERSHIP_ENTITY = 'ownership_entity';

export function CheckOwnership(entity: ObjectType<{ userId: string }>): ClassDecorator {
  return (target) => {
    Reflect.defineMetadata(OWNERSHIP_ENTITY, entity, target);
  };
}

@Injectable()
export class OwnershipGuard implements CanActivate {
  constructor(
    private reflector: Reflector,
    private dataSource: DataSource,
  ) {}

  async canActivate(context: ExecutionContext): Promise<boolean> {
    const entity = this.reflector.get(OWNERSHIP_ENTITY, context.getClass());
    if (!entity) return true;

    const request = context.switchToHttp().getRequest();
    const userId = request.user?.id;
    const resourceId = request.params.id;

    if (!userId || !resourceId) return false;

    const repository = this.dataSource.getRepository(entity);
    const resource = await repository.findOne({ where: { id: resourceId } });

    if (!resource) {
      throw new NotFoundException('Resource not found');
    }

    if ((resource as { userId: string }).userId !== userId) {
      throw new ForbiddenException('Access denied');
    }

    return true;
  }
}
```

### Usage

```typescript
@CheckOwnership(Event)
@UseGuards(JwtAuthGuard, OwnershipGuard)
@Controller('events')
export class EventsController {
  // All routes automatically check ownership for :id params
}
```

---

## Input Sanitization

### HTML Strip Decorator

```typescript
import { Transform } from 'class-transformer';

export function StripHtml(): PropertyDecorator {
  return Transform(({ value }) => {
    if (typeof value !== 'string') return value;
    return value.replace(/<[^>]*>/g, '').trim();
  });
}

// Usage:
export class CreateEventDto {
  @StripHtml()
  @IsString()
  @MaxLength(200)
  name: string;

  @StripHtml()
  @IsOptional()
  @IsString()
  @MaxLength(500)
  notes?: string;
}
```

### Vietnamese Name Validator

```typescript
import { registerDecorator, ValidationOptions } from 'class-validator';

export function IsVietnameseName(validationOptions?: ValidationOptions) {
  return function (object: object, propertyName: string): void {
    registerDecorator({
      name: 'isVietnameseName',
      target: object.constructor,
      propertyName,
      options: {
        message: 'Name contains invalid characters',
        ...validationOptions,
      },
      validator: {
        validate(value: string): boolean {
          if (!value) return false;
          // Allow Vietnamese characters, spaces, common punctuation
          return /^[\p{L}\p{M}\s\-'.()]+$/u.test(value);
        },
      },
    });
  };
}
```

---

## API Security Middleware

### Rate Limiting Setup

```typescript
// backend/src/app.module.ts
import { ThrottlerModule } from '@nestjs/throttler';

@Module({
  imports: [
    ThrottlerModule.forRoot([{
      ttl: 60000,   // 1 minute window
      limit: 100,   // 100 requests per minute
    }]),
  ],
})
export class AppModule {}
```

### Helmet & CORS Setup

```typescript
// backend/src/main.ts
import * as helmet from 'helmet';

async function bootstrap(): Promise<void> {
  const app = await NestFactory.create(AppModule);

  // Security headers
  app.use(helmet());

  // CORS
  app.enableCors({
    origin: process.env.CORS_ORIGIN?.split(',') || ['http://localhost:3000'],
    methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
    credentials: true,
  });

  // Global validation
  app.useGlobalPipes(new ValidationPipe({
    whitelist: true,
    forbidNonWhitelisted: true,
    transform: true,
  }));

  await app.listen(process.env.PORT || 3000);
}
```

---

## Security Testing Patterns

### Auth Tests

```typescript
describe('Auth Security', () => {
  it('should reject request without token', async () => {
    await request(app.getHttpServer())
      .get('/events')
      .expect(401);
  });

  it('should reject expired token', async () => {
    const expiredToken = generateExpiredToken();
    await request(app.getHttpServer())
      .get('/events')
      .set('Authorization', `Bearer ${expiredToken}`)
      .expect(401);
  });

  it('should reject access to other user resources', async () => {
    const user1Token = await loginAs('user1@test.com');
    const user2Event = await createEventAs('user2@test.com');

    await request(app.getHttpServer())
      .get(`/events/${user2Event.id}`)
      .set('Authorization', `Bearer ${user1Token}`)
      .expect(403);
  });
});
```

### Input Validation Tests

```typescript
describe('Input Validation', () => {
  it('should strip HTML from input', async () => {
    const response = await request(app.getHttpServer())
      .post('/events')
      .set('Authorization', `Bearer ${token}`)
      .send({ name: '<script>alert("xss")</script>Giỗ ông', type: 'gio' })
      .expect(201);

    expect(response.body.data.name).toBe('alert("xss")Giỗ ông');
  });

  it('should reject SQL injection in query', async () => {
    await request(app.getHttpServer())
      .get('/events?search=\'; DROP TABLE events; --')
      .set('Authorization', `Bearer ${token}`)
      .expect(200); // Should return empty results, not crash
  });

  it('should reject oversized input', async () => {
    await request(app.getHttpServer())
      .post('/events')
      .set('Authorization', `Bearer ${token}`)
      .send({ name: 'x'.repeat(201), type: 'gio' })
      .expect(400);
  });
});
```
