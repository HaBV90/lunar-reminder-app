# System Overview

**Related Documentation:**
- [Design Principles](./design-principles.md)
- [Tech Stack](./tech-stack.md)
- [Frontend Architecture](./frontend-architecture.md)
- [Backend Architecture](./backend-architecture.md)
- [Deployment](./deployment.md)

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     MOBILE CLIENTS                           │
│  ┌──────────────────────┐    ┌──────────────────────┐      │
│  │    iOS App           │    │   Android App         │      │
│  │  (React Native)      │    │  (React Native)       │      │
│  └──────────────────────┘    └──────────────────────┘      │
│             │                            │                   │
│             └────────────────────────────┘                   │
│                          │                                   │
└──────────────────────────┼───────────────────────────────────┘
                           │
                           │ HTTPS/REST API
                           │
┌──────────────────────────▼───────────────────────────────────┐
│              CLOUDFLARE TUNNEL (Expose)                      │
└──────────────────────────┬───────────────────────────────────┘
                           │
┌──────────────────────────▼───────────────────────────────────┐
│                  LOCAL BACKEND SERVER                         │
│  ┌────────────────────────────────────────────────┐          │
│  │          NestJS API Server                     │          │
│  │  ┌──────────────┐  ┌──────────────┐           │          │
│  │  │ Auth Module  │  │ Event Module │           │          │
│  │  └──────────────┘  └──────────────┘           │          │
│  │  ┌──────────────┐  ┌──────────────┐           │          │
│  │  │ Lunar Module │  │ Notif Module │           │          │
│  │  └──────────────┘  └──────────────┘           │          │
│  └────────────────────────────────────────────────┘          │
│                          │                                   │
│  ┌────────────────────────────────────────────────┐          │
│  │         PostgreSQL Database                     │          │
│  │  - Users, Events, Groups, Shares               │          │
│  └────────────────────────────────────────────────┘          │
│                          │                                   │
│  ┌────────────────────────────────────────────────┐          │
│  │         Redis Cache (Optional)                  │          │
│  │  - Session cache, API cache                    │          │
│  └────────────────────────────────────────────────┘          │
└───────────────────────────────────────────────────────────────┘
                           │
┌──────────────────────────▼───────────────────────────────────┐
│              EXTERNAL SERVICES                                │
│  ┌──────────────────┐    ┌──────────────────┐               │
│  │   OneSignal      │    │  Google Calendar │               │
│  │  (Push Notif)    │    │     API          │               │
│  └──────────────────┘    └──────────────────┘               │
└───────────────────────────────────────────────────────────────┘
```

---

## System Components

### Mobile App (React Native)

- Cross-platform iOS/Android
- Offline-first architecture
- Local storage (SQLite/Realm)
- Redux Toolkit state management
- React Navigation routing

### Backend API (NestJS)

- RESTful API
- JWT authentication
- TypeORM for database
- Modular architecture
- Scheduled jobs for notifications

### Database (PostgreSQL)

- Primary data store
- ACID transactions
- Full-text search
- JSON support for metadata

### Cache Layer (Redis)

- Session storage
- API response caching
- Rate limiting counters

### External Services

- **OneSignal:** Push notifications
- **Google Calendar API:** Export functionality
- **Cloudflare Tunnel:** Expose local server
