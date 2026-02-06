# Merge Request Checklist

Use this checklist to ensure quality before submitting and merging pull requests.

---

## For Developer

Before requesting review:

- [ ] Code compiles without errors
- [ ] All tests pass locally
- [ ] Lint/format checks pass (`npm run lint`)
- [ ] Feature matches task requirements
- [ ] Edge cases handled
- [ ] Error handling implemented
- [ ] No sensitive data in code (API keys, passwords, tokens)
- [ ] No `console.log` or debug code left in
- [ ] Self-review completed

---

## For Reviewer

When reviewing code:

### Code Quality
- [ ] Code is readable and maintainable
- [ ] Logic is correct and efficient
- [ ] Naming conventions followed (camelCase, PascalCase)
- [ ] No code duplication
- [ ] Functions are focused (single responsibility)

### Testing
- [ ] Tests cover main scenarios
- [ ] Edge cases tested
- [ ] Test coverage meets threshold (>80%)

### Security
- [ ] No security vulnerabilities (SQL injection, XSS, etc.)
- [ ] Sensitive data not exposed
- [ ] Authentication/authorization properly checked

### Performance
- [ ] No obvious performance issues
- [ ] Database queries optimized
- [ ] No memory leaks (React Native specific)

### Platform Specific

**Frontend (React Native):**
- [ ] UI matches design specifications
- [ ] Works on both iOS and Android
- [ ] Accessibility labels added
- [ ] No anonymous functions in render
- [ ] Proper memoization where needed

**Backend (NestJS):**
- [ ] API contract followed
- [ ] DTOs validated
- [ ] Proper HTTP status codes
- [ ] Error responses consistent

---

## Before Merge

Final checks:

- [ ] All review comments addressed
- [ ] Branch is up to date with target branch (rebase if needed)
- [ ] CI pipeline passes
- [ ] Documentation updated (if API or behavior changed)
- [ ] Changelog entry added (if significant change)

---

## PR Description Template

Include in your PR description:

```markdown
## What
Brief description of changes

## Why
Link to Jira task or issue: TASK-XXX

## How
Key implementation details

## Testing
- [ ] Unit tests
- [ ] Manual testing
- [ ] Tested on iOS
- [ ] Tested on Android

## Screenshots
(for UI changes)
```

---

## Quick Reference

### Commit Message Format

```
type(scope): description

feat(mobile): add login screen
fix(api): resolve token refresh bug
refactor(shared): extract validation utils
test(events): add unit tests for event service
chore: update dependencies
```

### Branch Naming

```
feature/TASK-123-add-login-screen
bugfix/TASK-456-fix-calendar-crash
hotfix/TASK-789-critical-auth-fix
refactor/TASK-101-cleanup-event-module
```

---

**See Also:**
- [Git Workflow](../../CLAUDE.md#git-workflow--branching-rules)
- [Coding Standards](../../CLAUDE.md#coding-standards)
