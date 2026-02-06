# Lunar Reminder App - Documentation Hub

**Vietnamese lunar calendar reminder app** helping users track important events like death anniversaries (giỗ), birthdays, weddings, and traditional holidays according to the lunar calendar.

---

## Quick Navigation

### Requirements
- [Requirements Overview](./requirements/index.md) - Product requirements and user stories
  - [Product Overview](./requirements/overview.md)
  - [User Personas](./requirements/personas.md)
  - [Use Cases](./requirements/use-cases.md)
  - [Feature Requirements](./requirements/features/)
  - [Non-Functional Requirements](./requirements/non-functional.md)

### Architecture & Technical
- [Architecture](./architecture/index.md) - System design and tech stack
  - [System Overview](./architecture/system-overview.md)
  - [Design Principles](./architecture/design-principles.md)
  - [Tech Stack](./architecture/tech-stack.md)
  - [Frontend Architecture](./architecture/frontend-architecture.md)
  - [Backend Architecture](./architecture/backend-architecture.md)
  - [Deployment](./architecture/deployment.md)

### API Reference
- [API Documentation](./api/index.md) - REST API endpoints
  - [Authentication](./api/auth-endpoints.md)
  - [Events](./api/events-endpoints.md)
  - [Groups](./api/groups-endpoints.md)
  - [Notifications](./api/notifications-endpoints.md)
  - [Sync](./api/sync-endpoints.md)
  - [Error Codes](./api/error-codes.md)

### Data Model
- [Data Model](./data-model/index.md) - Database schema and entities
  - [Tables](./data-model/tables/)
  - [Enums](./data-model/enums.md)
  - [Migrations](./data-model/migrations.md)

### Feature Implementations
- [Features](./features/) - Implementation details
  - [Lunar Calendar Algorithm](./features/lunar-calendar-algorithm.md)
  - [Notification System](./features/notification-system.md)
  - [Data Sync](./features/data-sync.md)
  - [Security](./features/security.md)

### UI/UX
- [UI/UX Specification](./ui-ux/index.md) - Design system and screens

### Guides
- [Backend Setup Guide](./guides/BACKEND_SETUP.md) - NestJS backend setup
- [Frontend Setup Guide](./guides/FRONTEND_SETUP.md) - React Native app setup
- [MR Checklist](./guides/MR_CHECKLIST.md) - Pull request review checklist

### Testing
- [Frontend Testing Plan](./testing/FRONTEND_TESTING_PLAN.md) - Testing strategy and sprint schedule

### Process
- [Implementation Workflow](./process/IMPLEMENTATION_WORKFLOW.md) - Spec → Code → PR flow
- [Project Lifecycle Checklist](./process/PROJECT_LIFECYCLE_CHECKLIST.md) - Full project checklist
- [Reorganization Changelog](./process/REORGANIZATION_CHANGELOG.md) - Structure changes log

### Reference
- [Glossary](./reference/glossary.md) - Vietnamese terms and definitions
- [Changelog](./changelog/index.md) - Version history
- [Architecture Decisions](./changelog/decisions/) - ADRs

### Implementation Specs (Source of Truth)
- [`openspec/specs/capabilities/`](../openspec/specs/capabilities/) - Capability specifications
  - [Auth](../openspec/specs/capabilities/auth/spec.md), [Events](../openspec/specs/capabilities/events/spec.md), [Calendar](../openspec/specs/capabilities/calendar/spec.md), [Groups](../openspec/specs/capabilities/groups/spec.md), [Notifications](../openspec/specs/capabilities/notifications/spec.md), [Settings](../openspec/specs/capabilities/settings/spec.md)

### Archive
- [`_archive/`](./_archive/) - Legacy monolith documents (read-only, historical reference)

---

## Document Status

| Document | Status | Last Updated |
|----------|--------|--------------|
| Requirements | ✅ Complete | 2026-02-02 |
| Technical Spec | ✅ Complete | 2026-02-02 |
| UI/UX Spec | ✅ Complete | 2026-02-02 |
| Data Model | ✅ Complete | 2026-02-02 |
| API Spec | ✅ Complete | 2026-02-02 |
| Glossary | ✅ Complete | 2026-02-02 |
| Setup Guides | ✅ Complete | 2026-02-04 |
| Testing Plan | ✅ Complete | 2026-02-04 |

---

## Tech Stack Summary

- **Frontend:** React Native + TypeScript + Redux Toolkit
- **Backend:** NestJS + TypeScript + TypeORM + PostgreSQL
- **Push Notifications:** OneSignal
- **Authentication:** Custom JWT

---

## Getting Started

1. Read [Product Overview](./requirements/overview.md) for project vision
2. Review [User Personas](./requirements/personas.md) to understand target users
3. Check [Tech Stack](./architecture/tech-stack.md) for technology decisions
4. **Set up development:**
   - [Backend Setup Guide](./guides/BACKEND_SETUP.md)
   - [Frontend Setup Guide](./guides/FRONTEND_SETUP.md)
5. See [API Documentation](./api/index.md) for endpoint details

---

**Last Updated:** 2026-02-04
