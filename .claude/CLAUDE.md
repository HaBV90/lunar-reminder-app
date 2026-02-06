# Claude Instructions for Lunar Reminder App

This file extends the root [CLAUDE.md](../CLAUDE.md) with project-specific context.

---

## Quick Reference

**Primary Instructions:** See [../CLAUDE.md](../CLAUDE.md) for:
- Code style & conventions
- Git workflow & branching rules
- Workflow sequence for new features/bugs
- Permissions & safety rules

---

## Roles

See **[roles.md](./roles.md)** for complete definitions:

**Claude Roles (8):**
1. Senior React/React Native Developer — Frontend code
2. Database Architect — Schema, entities, migrations
3. Test Engineer — Unit, integration, E2E tests
4. Security Reviewer — Auth, validation, OWASP
5. Git Master — Branches, commits, PRs
6. CI/CD Engineer — Pipelines, Docker, deployment
7. Project Assistant — Task tracking, notifications
8. Reviewer Simulator — Pre-commit self-review

**Human Roles (3):**
1. Product Owner (Ha) — Requirements, approvals
2. UX Reviewer — UI/design review
3. Code Reviewer — PR review and merge

---

## Rules Overview

All rules: **[rules/index.md](./rules/index.md)**

| Rule File | Scope |
|-----------|-------|
| [Frontend](../frontend/.claude/rules.md) | React Native, TypeScript, styling |
| [Backend](../backend/.claude/rules.md) | NestJS, DTOs, entities |
| [Database](./rules/database.md) | PostgreSQL, TypeORM, migrations |
| [Testing](./rules/testing.md) | Coverage, patterns, mocking |
| [Security](./rules/security.md) | JWT, authorization, validation |
| [Git & DevOps](./rules/git-devops.md) | Branches, CI/CD, Docker |

**Priority:** Security > Testing > Area-specific > Style

---

## Available Skills

| Command | Description |
|---------|------------|
| `/frontend` | RN components, hooks, styling |
| `/backend:index` | NestJS modules, services |
| `/backend:entity-template` | TypeORM entity scaffold |
| `/backend:module-template` | NestJS module scaffold |
| `/backend:service-template` | Service with CRUD |
| `/backend:controller-template` | Controller with Swagger |
| `/backend:dto-template` | DTO with validation |
| `/backend:common-patterns` | Guards, filters, pipes |
| `/database` | Entities, migrations, queries |
| `/testing` | Test templates, mocks |
| `/security` | Auth, ownership, validation |
| `/devops` | CI/CD, Docker, linting |
| `/lunar-calendar` | Lunar date conversion |
| `/notifications` | Push notifications |
| `/opsx:*` | OpenSpec workflow commands |

---

## Session Workflow

### On Resume

1. Read `current-state.md` to understand where you left off
2. Check git status for uncommitted changes
3. Continue from last step

### During Work

1. Update task status as you progress
2. Commit after each meaningful change
3. Self-review before commit (Reviewer Simulator role)

### On Completion

1. Create PR with description
2. Update `current-state.md`
3. Notify Ha: "Task hoàn thành. PR link: [link]."

---

## File Map

| File | Governs |
|------|---------|
| `CLAUDE.md` (root) | Standards, workflow, permissions |
| `.claude/CLAUDE.md` | Session context, roles, skills |
| `.claude/roles.md` | Role definitions + RACI |
| `.claude/rules/` | All coding rules (6 files) |
| `.claude/commands/` | All skills/slash commands |
| `.claude/current-state.md` | Current task progress |
| `frontend/.claude/rules.md` | Frontend-specific rules |
| `backend/.claude/rules.md` | Backend-specific rules |

---

## Session Resume

When resuming work, always read `.claude/current-state.md` first to understand:
- Current task in progress
- Last completed step
- Any blockers or notes

---

## Key Commands

```bash
# Frontend
cd frontend && npm run ios
cd frontend && npm test

# Backend
cd backend && npm run start:dev
cd backend && npm test
```

---

See [../CLAUDE.md](../CLAUDE.md) for complete guidelines.
