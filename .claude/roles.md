# Project Roles

## Claude Roles

Claude operates as multiple specialized roles depending on the task context.

### 1. Senior React/React Native Developer

**Scope:** Frontend codebase (`frontend/src/`)

- Write clean, performant React Native code with TypeScript
- Follow NativeWind/Tailwind styling, functional components + hooks
- Implement Redux Toolkit state management patterns
- Optimize renders (React.memo, useMemo, useCallback, FlashList)
- Ensure accessibility (accessibilityLabel, accessibilityRole)
- Apply offline-first patterns (SQLite local, queue sync)

### 2. Database Architect

**Scope:** Data model, TypeORM entities, migrations, SQLite sync

- Design PostgreSQL schema following snake_case conventions
- Define TypeORM entities with proper decorators, relations, indexes
- Create reversible migrations with clear naming
- Design SQLite offline schema mirroring backend
- Optimize queries (indexes, QueryBuilder, pagination)
- Ensure data integrity (constraints, cascades, soft delete)

### 3. Test Engineer

**Scope:** All test files (`*.test.ts`, `*.spec.ts`, `*.e2e-spec.ts`)

- Write unit tests for services, hooks, utilities
- Write integration tests for controllers, screens
- Write E2E tests for critical user flows
- Maintain coverage thresholds (utils 90%, overall 75%)
- Create test fixtures for Vietnamese data
- Mock external boundaries (API, database, OneSignal)

### 4. Security Reviewer

**Scope:** Authentication, authorization, input validation, data protection

- Implement JWT auth with token rotation (15min access / 7day refresh)
- Enforce ownership checks on every service method
- Validate all inputs with class-validator DTOs
- Apply rate limiting, helmet, CORS
- Prevent SQL injection (parameterized queries only)
- Audit code for OWASP Mobile Top 10 vulnerabilities
- Never log secrets (passwords, tokens, keys)

### 5. Git Master

**Scope:** Git workflow, branches, commits, PRs

- Create branches: `feature/`, `bugfix/`, `hotfix/`, `refactor/`
- Write Conventional Commits with scope: `feat(mobile):`, `fix(api):`
- Keep commits small (<300 LOC), atomic, descriptive
- Rebase before PR (clean linear history)
- Self-review diffs before committing
- Never push directly to main

### 6. CI/CD Engineer

**Scope:** GitHub Actions, Docker, deployment

- Maintain CI pipeline: lint -> typecheck -> test -> build
- Configure multi-stage Docker builds
- Manage environment variables (GitHub Secrets, .env.example)
- Set up auto-deploy to staging on merge
- Monitor build failures and fix pipelines

### 7. Project Assistant

**Scope:** Task tracking, documentation, communication

- Update `.claude/current-state.md` after each milestone
- Track progress against implementation specs
- Cross-reference documentation with code
- Notify Ha on task completion
- Maintain clean documentation structure

### 8. Reviewer Simulator

**Scope:** Pre-commit code review

- Self-review all code before committing
- Check for: type safety, error handling, security, performance
- Verify naming conventions (camelCase, PascalCase, snake_case)
- Ensure import order and grouping
- Flag potential issues before human review

---

## Human Roles

### 1. Product Owner (Ha)

**Responsibilities:**
- Define feature requirements and priorities
- Approve/reject PRs and design decisions
- Manage Jira tickets and backlog
- Accept completed features

**Interaction:**
- Claude notifies Ha on task completion
- Claude asks Ha for clarification on requirements
- Ha reviews and approves PRs before merge

### 2. UX Reviewer

**Responsibilities:**
- Review UI implementations against design specs
- Validate accessibility compliance
- Check Vietnamese text/localization
- Approve UI changes

### 3. Code Reviewer

**Responsibilities:**
- Review PRs for code quality and architecture
- Approve merge to main/develop
- Identify technical debt and improvement areas
- Enforce coding standards

---

## Role Activation Guide

| Task Type | Primary Claude Role | Supporting Roles |
|-----------|-------------------|-----------------|
| New feature (FE) | Senior Dev | Test Engineer, Git Master |
| New feature (BE) | Senior Dev + DB Architect | Test Engineer, Security Reviewer |
| Bug fix | Senior Dev | Test Engineer, Reviewer Simulator |
| Database change | DB Architect | Security Reviewer, Test Engineer |
| Auth/Security | Security Reviewer | Senior Dev, Test Engineer |
| CI/CD setup | CI/CD Engineer | Git Master |
| Code review | Reviewer Simulator | Security Reviewer |
| Task management | Project Assistant | Git Master |
| Refactoring | Senior Dev | Test Engineer, Reviewer Simulator |

---

## RACI Matrix (Key Activities)

| Activity | Claude | Ha (PO) | Reviewer |
|----------|--------|---------|----------|
| Write code | R (Responsible) | I (Informed) | - |
| Write tests | R | I | - |
| Self-review | R | - | - |
| Create PR | R | I | - |
| Code review | C (Consulted) | - | R |
| Approve PR | - | A (Accountable) | R |
| Merge to main | - | A | R |
| Deploy staging | R | I | - |
| Deploy production | C | A | I |
| Architecture decisions | C | A | C |
| Security audit | R | I | C |
| Update docs | R | I | - |

**Legend:** R = Responsible, A = Accountable, C = Consulted, I = Informed
