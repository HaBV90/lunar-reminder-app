# Architecture Documentation

Technical architecture and design documentation for the Lunar Reminder App.

---

## Overview

The Lunar Reminder App follows an **offline-first architecture** with a React Native mobile frontend and NestJS backend API. The system is designed for:

- Cross-platform mobile (iOS & Android)
- Offline functionality with background sync
- Scalable from local deployment to cloud

---

## Documents

### System Design
- [System Overview](./system-overview.md) - High-level architecture diagram and components
- [Design Principles](./design-principles.md) - Architecture principles and patterns
- [Tech Stack](./tech-stack.md) - Technology choices and versions

### Platform Architecture
- [Frontend Architecture](./frontend-architecture.md) - React Native app structure
- [Backend Architecture](./backend-architecture.md) - NestJS module organization

### Infrastructure
- [Deployment](./deployment.md) - Local setup, Cloudflare Tunnel, cloud migration

---

## Key Architectural Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Mobile Platform | React Native | Cross-platform, JS ecosystem |
| Backend Framework | NestJS | TypeScript-first, structured |
| Database | PostgreSQL | ACID, JSON support |
| State Management | Redux Toolkit | Industry standard, great tooling |
| Local Storage | SQLite | Simple, familiar SQL |
| Push Notifications | OneSignal | Free tier, easy integration |

See [Architecture Decision Records](../changelog/decisions/) for detailed rationales.

---

## System Components

```
┌─────────────────────────────────────┐
│         MOBILE CLIENTS              │
│  iOS App         Android App        │
│      (React Native)                 │
└──────────────┬──────────────────────┘
               │ HTTPS/REST API
┌──────────────▼──────────────────────┐
│      CLOUDFLARE TUNNEL              │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│        BACKEND SERVER               │
│  NestJS API → PostgreSQL → Redis    │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│      EXTERNAL SERVICES              │
│  OneSignal    Google Calendar       │
└─────────────────────────────────────┘
```

---

## Related Documentation

- [Data Model](../data-model/index.md) - Database schema
- [API Reference](../api/index.md) - REST endpoints
- [Features](../features/) - Implementation details

---

**Last Updated:** 2026-02-03
