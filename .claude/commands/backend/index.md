# Backend Development Skill

Patterns and templates for NestJS + TypeScript development in the Lunar Reminder App.

---

## Module Structure

Every feature module follows this pattern:

```
modules/
└── events/
    ├── events.module.ts      # Module definition
    ├── events.controller.ts  # HTTP endpoints
    ├── events.service.ts     # Business logic
    ├── entities/
    │   └── event.entity.ts   # TypeORM entity
    └── dto/
        ├── create-event.dto.ts
        ├── update-event.dto.ts
        └── find-events.dto.ts
```

---

## Templates

- [Module Template](./module-template.md) - NestJS module structure
- [Controller Template](./controller-template.md) - REST endpoints
- [Service Template](./service-template.md) - Business logic
- [Entity Template](./entity-template.md) - TypeORM entities
- [DTO Template](./dto-template.md) - Request validation
- [Common Patterns](./common-patterns.md) - Response format, error handling

---

## Path Aliases

Configure in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@modules/*": ["src/modules/*"],
      "@common/*": ["src/common/*"],
      "@config/*": ["src/config/*"],
      "@database/*": ["src/database/*"]
    }
  }
}
```

---

## Quick Reference

### Creating a New Module

```bash
# Generate module
nest g module modules/events
nest g controller modules/events
nest g service modules/events
```

### Common Imports

```typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { JwtAuthGuard } from '@common/guards/jwt-auth.guard';
import { CurrentUser } from '@common/decorators/current-user.decorator';
```

---

## Related

- [Architecture](../../docs/architecture/backend-architecture.md)
- [API Reference](../../docs/api/index.md)
