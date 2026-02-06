# Rules Index

Central reference for all project rules and coding standards.

## Area-Specific Rules

| Rule File | Scope | Key Topics |
|-----------|-------|-----------|
| [Frontend Rules](../../frontend/.claude/rules.md) | `frontend/src/` | React Native, TypeScript, styling, components, hooks |
| [Backend Rules](../../backend/.claude/rules.md) | `backend/src/` | NestJS, DTOs, entities, controllers, services |
| [Database Rules](./database.md) | Data layer | PostgreSQL, TypeORM, migrations, SQLite offline |
| [Testing Rules](./testing.md) | All tests | Coverage, patterns, mocking, fixtures |
| [Security Rules](./security.md) | Auth & safety | JWT, authorization, validation, OWASP |
| [Git & DevOps Rules](./git-devops.md) | Workflow | Branches, commits, PRs, CI/CD, Docker |

## Cross-Cutting Rules (from root CLAUDE.md)

- **TypeScript:** Strict mode, no `any`, explicit return types
- **Naming:** camelCase functions/variables, PascalCase components/types, snake_case DB columns
- **Imports:** React -> RN -> third-party -> internal (absolute) -> relative
- **Commits:** Conventional Commits with scope
- **LOC limit:** < 300 LOC per change

## Rule Priority

When rules conflict, apply in this order:

1. **Security** — Security rules always win (never compromise auth, validation, or data protection)
2. **Testing** — Tests must pass before commit
3. **Area-specific** — Frontend/Backend/Database rules for their respective domains
4. **Style** — Formatting and naming conventions
5. **Convenience** — Developer experience improvements

## Quick Decision Guide

| Question | Rule File |
|----------|-----------|
| How to name a database column? | [Database](./database.md) |
| What coverage do I need? | [Testing](./testing.md) |
| How to store JWT tokens? | [Security](./security.md) |
| What branch name format? | [Git & DevOps](./git-devops.md) |
| How to structure a component? | [Frontend](../../frontend/.claude/rules.md) |
| How to write a DTO? | [Backend](../../backend/.claude/rules.md) |
