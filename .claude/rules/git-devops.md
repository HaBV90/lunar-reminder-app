# Git & DevOps Rules

## Branch Strategy

### Branch Types

| Branch | Purpose | Base | Merge to |
|--------|---------|------|----------|
| `main` | Production-ready | - | - |
| `feature/XXX-desc` | New features | main | main (via PR) |
| `bugfix/XXX-desc` | Bug fixes | main | main (via PR) |
| `hotfix/XXX-desc` | Urgent production fixes | main | main (via PR) |
| `refactor/XXX-desc` | Code refactoring | main | main (via PR) |

### Rules

1. **Never** commit directly to `main`
2. **Always** create a new branch for each task
3. Branch from latest `main` (pull before branching)
4. Delete branch after merge
5. Keep branches short-lived (< 1 week ideally)

---

## Commit Rules

### Conventional Commits (Required)

```
type(scope): description

feat(mobile): add event creation form
fix(api): resolve token refresh race condition
refactor(shared): extract lunar date utilities
test(api): add events service unit tests
chore: update dependencies
docs: add API endpoint documentation
```

### Types

| Type | When |
|------|------|
| `feat` | New feature |
| `fix` | Bug fix |
| `refactor` | Code restructure (no behavior change) |
| `test` | Add/update tests |
| `chore` | Tooling, deps, config |
| `docs` | Documentation only |
| `style` | Formatting (no logic change) |
| `perf` | Performance improvement |
| `ci` | CI/CD changes |

### Commit Message Rules

1. Subject line: max 72 characters
2. Use imperative mood: "add feature" not "added feature"
3. Explain **why** in body if not obvious from subject
4. Reference Jira ticket: `ABC-123 - Implement login flow`
5. One logical change per commit
6. No WIP commits on main (squash before merge)

---

## PR Rules

### Before Creating PR

1. Rebase on latest main: `git fetch origin && git rebase origin/main`
2. Self-review all changes: `git diff origin/main...HEAD`
3. Run lint: `npm run lint`
4. Run typecheck: `npm run typecheck`
5. Run tests: `npm test`
6. Ensure all checks pass

### PR Requirements

- **Max 300 LOC** changed (excluding generated files)
- **Draft mode** first, then ready for review
- **Title:** `[JIRA-ID] Short description` or Conventional format
- **Description:**
  - **What:** Summary of changes
  - **Why:** Link to issue/ticket
  - **How:** Key implementation decisions
  - **Tests:** What was tested
  - Screenshots/videos for UI changes

### Merge Strategy

- **Squash and merge** for feature branches (clean history)
- **Rebase and merge** for long-lived branches
- **Never** merge commit (keeps history linear)
- Delete source branch after merge

---

## CI/CD Pipeline

### Pipeline Stages

```
push/PR → Install → Lint → Typecheck → Test → Build
```

### Stage Details

| Stage | Command | Fails on |
|-------|---------|----------|
| Install | `npm ci` | Lockfile mismatch |
| Lint | `npm run lint` | Any lint error |
| Typecheck | `npx tsc --noEmit` | Any type error |
| Test | `npm test -- --coverage` | Failed test or below threshold |
| Build | `npm run build` | Build error |

### Pipeline Rules

1. All stages must pass before merge is allowed
2. Cache `node_modules` between runs
3. Run backend and frontend pipelines in parallel
4. Use Node 20 LTS
5. Fail fast — stop on first failure in stage

---

## Environment Management

### Environment Files

```
.env.example     # Template with placeholder values (committed)
.env             # Local development (in .gitignore)
.env.test        # Test environment (in .gitignore)
```

### Required Variables

```env
# .env.example
DATABASE_URL=postgresql://user:password@localhost:5432/lunar_reminder
JWT_SECRET=your-jwt-secret-here
JWT_REFRESH_SECRET=your-refresh-secret-here
ONESIGNAL_APP_ID=your-onesignal-app-id
ONESIGNAL_API_KEY=your-onesignal-api-key
NODE_ENV=development
PORT=3000
```

### Secret Management

- **Local:** `.env` files (never committed)
- **CI:** GitHub Secrets
- **Staging/Production:** Cloud secret manager (AWS SSM, etc.)
- **Rule:** If a value is secret, it must NEVER appear in code or logs

### Environments

| Environment | Purpose | Deploy Trigger |
|------------|---------|---------------|
| Development | Local dev | Manual |
| Staging | Pre-production testing | Auto on merge to main |
| Production | Live users | Manual approval |

---

## Docker

### Dockerfile (Backend)

- Multi-stage build: builder + production
- Base image: `node:20-alpine`
- Run as non-root user
- Copy only production dependencies
- Health check endpoint

### docker-compose.yml

- Services: api, postgres, pgadmin (dev only)
- Named volumes for data persistence
- Network isolation
- Environment variables from `.env`

---

## Monitoring & Health

### Health Check Endpoint

- `GET /api/health` — Returns service status
- Check: database connection, external service connectivity
- Used by Docker health check and load balancer

### Logging

- Structured JSON logs in production
- Log levels: error, warn, log, debug
- Never log: passwords, tokens, PII
- Include request ID for tracing
