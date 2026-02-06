# Project Kickoff Workflow

The single "start here" guide for onboarding to the Lunar Reminder App project.

---

## 1. Orientation

### Read First

1. **[CLAUDE.md](../../CLAUDE.md)** — Project overview, tech stack, coding standards, workflow rules
2. **[docs/reference/glossary.md](../reference/glossary.md)** — Vietnamese terminology (giỗ, lịch âm, Tết, etc.)
3. **[docs/index.md](../index.md)** — Documentation hub and navigation

### Understand the App

A Vietnamese lunar calendar reminder app that helps users:
- Track death anniversaries (giỗ), birthdays, weddings, traditional holidays
- Convert between lunar and solar dates automatically
- Receive push notifications before events
- Work offline with local data sync

### Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React Native + TypeScript + Redux Toolkit |
| Backend | NestJS + TypeScript + TypeORM |
| Database | PostgreSQL (server) + SQLite (mobile offline) |
| Notifications | OneSignal |
| Auth | Custom JWT (access + refresh tokens) |

---

## 2. Architecture

### Key Documentation

- **[docs/architecture/](../architecture/)** — Architecture Decision Records (ADRs)
- **[docs/data-model/](../data-model/)** — Database schema and entity relationships
- **[docs/requirements/](../requirements/)** — Feature specifications
- **[openspec/specs/capabilities/](../../openspec/specs/capabilities/)** — Implementation specs (source of truth)

### System Overview

```
┌─────────────────┐    HTTPS/JWT    ┌─────────────────┐    TypeORM    ┌──────────────┐
│  React Native   │ ◄────────────► │    NestJS API    │ ◄──────────► │  PostgreSQL   │
│  (Mobile App)   │                │    (Backend)     │              │  (Database)   │
└────────┬────────┘                └────────┬────────┘              └──────────────┘
         │                                  │
    ┌────┴─────┐                    ┌───────┴────────┐
    │  SQLite  │                    │   OneSignal    │
    │ (Offline)│                    │   (Push)       │
    └──────────┘                    └────────────────┘
```

---

## 3. Rules

All coding rules are organized by area: **[.claude/rules/index.md](../../.claude/rules/index.md)**

| Rule File | Key Topics |
|-----------|-----------|
| [Frontend](../../frontend/.claude/rules.md) | RN components, hooks, styling, accessibility |
| [Backend](../../backend/.claude/rules.md) | NestJS modules, DTOs, entities, Swagger |
| [Database](../../.claude/rules/database.md) | PostgreSQL, TypeORM, migrations, SQLite offline |
| [Testing](../../.claude/rules/testing.md) | Coverage thresholds, test patterns, mocking |
| [Security](../../.claude/rules/security.md) | JWT, authorization, validation, OWASP |
| [Git & DevOps](../../.claude/rules/git-devops.md) | Branches, commits, PRs, CI/CD |

**Priority order:** Security > Testing > Area-specific > Style

---

## 4. Roles

See **[.claude/roles.md](../../.claude/roles.md)** for complete definitions.

### Claude Roles (8)

1. **Senior Dev** — Write clean, performant code
2. **DB Architect** — Schema design, migrations, queries
3. **Test Engineer** — Unit, integration, E2E tests
4. **Security Reviewer** — Auth, validation, OWASP compliance
5. **Git Master** — Branch management, clean commits
6. **CI/CD Engineer** — Pipelines, Docker, deployment
7. **Project Assistant** — Task tracking, notifications
8. **Reviewer Simulator** — Pre-commit self-review

### Human Roles (3)

1. **Product Owner (Ha)** — Requirements, approvals, priorities
2. **UX Reviewer** — UI/design review
3. **Code Reviewer** — PR review and merge approval

---

## 5. Skills (Slash Commands)

| Category | Command | Purpose |
|----------|---------|---------|
| Frontend | `/frontend` | RN development patterns |
| Backend | `/backend:index` | NestJS overview |
| Backend | `/backend:entity-template` | TypeORM entity scaffold |
| Backend | `/backend:module-template` | NestJS module scaffold |
| Backend | `/backend:service-template` | Service with CRUD |
| Backend | `/backend:controller-template` | Controller with Swagger |
| Backend | `/backend:dto-template` | DTO with validation |
| Backend | `/backend:common-patterns` | Guards, filters, pipes |
| Database | `/database` | Entities, migrations, queries |
| Testing | `/testing` | Test templates, mocks |
| Security | `/security` | Auth, ownership, validation |
| DevOps | `/devops` | CI/CD, Docker, linting |
| Domain | `/lunar-calendar` | Lunar date conversion |
| Domain | `/notifications` | Push notification patterns |
| Workflow | `/opsx:*` | OpenSpec artifact workflow |

---

## 6. Environment Setup

### Backend Setup

```bash
cd backend
cp .env.example .env          # Configure local environment
npm install                    # Install dependencies
npm run db:migrate             # Run database migrations
npm run start:dev              # Start dev server (http://localhost:3000)
```

### Frontend Setup

```bash
cd frontend
npm install                    # Install dependencies
npx pod-install                # Install iOS pods
npm run ios                    # Run on iOS simulator
npm run android                # Run on Android emulator
```

### Prerequisites

- Node.js 20 LTS
- PostgreSQL 16+
- Xcode (for iOS)
- Android Studio (for Android)

---

## 7. Task Workflow (Quick Reference)

The full 12-step workflow from `CLAUDE.md`, condensed:

```
1. Plan      → Use Plan Mode, define approach
2. Document  → Record bug/feature details
3. Branch    → git checkout -b feature/XXX-description
4. Implement → Small diffs (<300 LOC)
5. Test      → Unit + integration tests
6. Lint      → ESLint + Prettier
7. Commit    → Conventional Commits
8. Push      → git push origin HEAD
9. PR        → gh pr create (draft first)
10. Track    → Update Jira/task tracker
11. Review   → Wait for human approval
12. Done     → Notify Ha, update status
```

---

## 8. Session Management

### current-state.md Lifecycle

**Location:** `.claude/current-state.md`

**Update after each milestone:**

```markdown
# Current State

- Current task: [JIRA-ID] - [description]
- Status: In Progress / Done
- Current step: [X/12]
- Branch: feature/XXX-description
- Last output: [summary of last action]
- Blockers: [none / description]
```

**On resume:** Always read this file first to continue where you left off.

---

## 9. MVP Checklist

### Phase 0: Foundation

- [ ] First commit on main
- [ ] Branch protection enabled
- [ ] CLAUDE.md + rules + roles in place
- [ ] `.env.example` for backend

### Phase 1: DevOps

- [ ] ESLint + Prettier configured (frontend + backend)
- [ ] GitHub Actions CI workflow
- [ ] Husky + lint-staged + commitlint
- [ ] Test scaffold (Jest configured, first passing test)

### Phase 2: Core Backend

- [ ] Auth module (register, login, refresh, logout)
- [ ] Users module (profile CRUD)
- [ ] Events module (CRUD + lunar date handling)
- [ ] Lunar calendar service (conversion logic)
- [ ] Database migrations for core tables
- [ ] API documentation (Swagger)

### Phase 3: Core Frontend

- [ ] Auth screens (login, register)
- [ ] Event list screen
- [ ] Event create/edit form
- [ ] Event detail screen
- [ ] Redux store setup (auth, events slices)
- [ ] Navigation structure

### Phase 4: Integration

- [ ] Frontend ↔ Backend API integration
- [ ] JWT token management (storage, refresh)
- [ ] Error handling (API errors → user messages)
- [ ] Loading states and empty states

### Phase 5: Notifications

- [ ] OneSignal SDK integration (frontend)
- [ ] Notification service (backend)
- [ ] Reminder scheduling (before events)
- [ ] Notification preferences

### Phase 6: Polish & Ship

- [ ] Group management (share events)
- [ ] Calendar view
- [ ] Settings screen
- [ ] Offline mode (SQLite + sync)
- [ ] Production deployment
- [ ] App Store / Play Store submission

---

## 10. Key Reference Links

| Resource | Location |
|----------|----------|
| Project instructions | `CLAUDE.md` |
| Claude context | `.claude/CLAUDE.md` |
| Role definitions | `.claude/roles.md` |
| Rules index | `.claude/rules/index.md` |
| Implementation specs | `openspec/specs/capabilities/` |
| Documentation hub | `docs/index.md` |
| Implementation workflow | `docs/process/IMPLEMENTATION_WORKFLOW.md` |
| Lifecycle checklist | `docs/process/PROJECT_LIFECYCLE_CHECKLIST.md` |
| Glossary | `docs/reference/glossary.md` |
| Design system | `docs/ui-ux/index.md` |
| Data model | `docs/data-model/` |
| Current progress | `.claude/current-state.md` |
