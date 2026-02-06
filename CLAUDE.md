# Lunar Reminder App - Claude Instructions

## Start Here

New to this project? Read the **[Project Kickoff Workflow](docs/process/PROJECT_KICKOFF_WORKFLOW.md)** for a complete onboarding guide.

## Project Overview

Vietnamese lunar calendar reminder app helping users track important events like death anniversaries (giỗ), birthdays, weddings, and traditional holidays according to the lunar calendar.

## Tech Stack

- **Frontend:** React Native + TypeScript + Redux Toolkit
- **Backend:** NestJS + TypeScript + TypeORM + PostgreSQL
- **Push Notifications:** OneSignal
- **Authentication:** Custom JWT

## Project Structure

```
frontend/src/       # React Native mobile app
backend/src/        # NestJS API server
docs/               # Specifications and documentation
```

## Roles

See **[.claude/roles.md](.claude/roles.md)** for complete role definitions (8 Claude roles + 3 Human roles with RACI matrix).

## Rules

All coding rules are organized by area. See **[.claude/rules/index.md](.claude/rules/index.md)** for the full index.

| Area | Rule File |
|------|-----------|
| Frontend | `frontend/.claude/rules.md` |
| Backend | `backend/.claude/rules.md` |
| Database | `.claude/rules/database.md` |
| Testing | `.claude/rules/testing.md` |
| Security | `.claude/rules/security.md` |
| Git & DevOps | `.claude/rules/git-devops.md` |

## Skills (Slash Commands)

| Command | Description |
|---------|------------|
| `/frontend` | Frontend development patterns |
| `/backend:index` | Backend development overview |
| `/database` | Database entities, migrations, queries |
| `/testing` | Test templates and mock patterns |
| `/security` | Auth, validation, security patterns |
| `/devops` | CI/CD, Docker, linting setup |
| `/lunar-calendar` | Lunar calendar conversion logic |
| `/notifications` | Push notification patterns |

## Workflow Rules (BẮT BUỘC tuân thủ 100%)

- Always plan first: Use Plan Mode trước khi code lớn
- Break tasks nhỏ: < 200-300 LOC per step
- Test trước commit: Chạy unit/integration tests nếu có
- Commit often, message descriptive + liên kết task
- Permissions: Always allow git _, npm _, expo \*, nhưng hỏi nếu rm/mv nguy hiểm
- Khi refactor: Tạo branch riêng, commit incremental

## Key Documentation

- `docs/index.md` - Documentation hub (start here)
- `docs/requirements/` - Feature requirements
- `docs/architecture/` - Architecture decisions
- `docs/ui-ux/index.md` - Design system
- `docs/data-model/` - Database schema
- `docs/reference/glossary.md` - Vietnamese terms and definitions
- `docs/process/IMPLEMENTATION_WORKFLOW.md` - Spec → Code → PR flow
- `openspec/specs/capabilities/` - Implementation specs (source of truth)

## Coding Standards

### TypeScript

- Strict mode enabled
- Use interfaces over types when possible
- Explicit return types on functions

### Frontend (React Native)

- Functional components with hooks
- Redux Toolkit for state management
- Path aliases: `@components/`, `@screens/`, `@services/`, `@store/`

### Backend (NestJS)

- Module-based architecture
- DTOs with class-validator
- Path aliases: `@modules/`, `@common/`, `@config/`

### Git

- Branch naming (Conventional + scope):
  - feature/[jira-ticket-id]-[short-description-kebab-case] ví dụ: feature/ABC-123-add-login-screen
  - bugfix/[jira-ticket-id]-[short-description]
  - hotfix/[jira-ticket-id]-[short-description] (chỉ cho production urgent)
  - refactor/[jira-ticket-id]-[short-description]
- Main branch: protected (require PR + review + status checks)
- Develop branch: Nếu dùng Git Flow đầy đủ (xem phần Best practices Git Flow bên dưới)
- Mỗi task/feature/bug → PHẢI tạo branch mới từ main/develop → KHÔNG code trực tiếp trên main/develop
- Commit: Conventional Commits + scope
  - feat(mobile): add login screen
  - fix(web): resolve auth token refresh bug
  - refactor(shared): extract useApi hook
  - test: add unit tests for cart reducer
  - chore: update deps
- Commit message: Mô tả rõ ràng, liên kết Jira (ví dụ: ABC-123 – Implement login flow)
- Rebase thường xuyên để giữ history sạch (KHÔNG merge commit lộn xộn)

### Code Style & Conventions (BẮT BUỘC tuân thủ 100%)

- TypeScript: strict mode, no 'any', explicit return types
- Components: Functional + hooks only, memoize khi cần (React.memo, useMemo, useCallback)
- Naming: camelCase variables/functions, PascalCase components/types/interfaces
- Styling: Tailwind classes / NativeWind only – KHÔNG inline styles, KHÔNG StyleSheet.create trừ trường hợp đặc biệt
- Imports: Absolute imports nếu monorepo (tsconfig paths), group imports: react → third-party → internal
- Error handling: Throw meaningful errors, use try/catch + Sentry/Logger nếu production
- Performance RN: Avoid anonymous functions in render, use FlashList thay FlatList nếu list dài, profile với Flipper/Reanimated dev tools
- Accessibility: Thêm accessible={true}, accessibilityLabel cho mọi interactive element

### Workflow Khi Tạo Tính Năng Mới / Fix Bug (Strict Sequence – KHÔNG ĐƯỢC BỎ BƯỚC)

1. **Plan first** → Luôn dùng Plan Mode hoặc prompt "Plan chi tiết task [mô tả] theo CLAUDE.md"
2. **Document** -> Luôn ghi lại doc để mô tả bug, nguyên nhân, solution fix lỗi. Tạo file checklist hoặc update để tránh gặp lại lần sau.
3. **Checkout branch mới** → Tự động: git checkout -b feature/ABC-123-xxx
4. **Implement** → Code incremental, small diffs (<300 LOC/change)
5. **Test** → Viết/run unit + integration tests trước commit. Nếu có E2E → run trước push
6. **Lint & format** → Chạy eslint + prettier auto-fix
7. **Commit** → Conventional message + liên kết Jira
8. **Push** → git push origin HEAD
9. **Tạo Merge Request (MR/PR)** → Sử dụng gh CLI (GitHub) hoặc gitlab CLI:
   - Title: [JIRA-123] Add login screen with biometric
   - Description:
     - What: [mô tả]
     - Why: [issue link]
     - How: [key changes]
     - Tests: [passed]
     - Screenshots/videos nếu UI
   - Draft mode đầu tiên
10. **Update Jira** → Comment vào ticket: "PR created: [link PR]" + "Waiting review" + assign reviewer
11. **Review cycle** → KHÔNG merge tự động. Chờ human review → approve → merge (squash hoặc rebase)
12. **Done** → Sau merge: comment Jira "Merged to develop/main. Ready for QA/deploy" + chuyển status Done/Resolved + notify bạn (Ha) qua Slack/email hoặc prompt "Thông báo cho Ha: task ABC-123 đã xong"

### Permissions & Safety Rules

- Always allow: Read, Write, Edit, Bash(git _), Bash(gh _), Bash(npm _ | pnpm _ | yarn _ | expo _), Bash(jest \*)
- Deny hoặc hỏi confirm: Bash(rm _), Bash(mv _ outside repo), Bash(git push --force)
- Không refactor lớn không yêu cầu
- Không thêm deps mới mà không hỏi
- Không delete file mà không confirm

### Skills to Auto-Load

- Frontend:
  - react-native-best-practices@callstack-agent-skills
  - react-best-practices@vercel-agent-skills
  - project-conventions (custom nếu có)

### MANDATORY FINAL STEP

Sau khi hoàn thành bất kỳ task nào (implement + PR + merge), PHẢI output rõ ràng:
"Task hoàn thành. PR/MR link: [link]. Đã update Jira [ticket-id]. Thông báo cho tôi: [tóm tắt ngắn gọn]. Ready for review/deploy."

### History current progress

Luôn lưu progress vào file .claude/current-state.md sau mỗi milestone lớn:

- Current task: [JIRA-ID] - [description]
- Status: In Progress / Done
- Current step: 4/10
- Last output: [tóm tắt]
  Khi resume, đọc file này trước để continue

## Vietnamese Context

This app serves Vietnamese users. Key concepts:

- **Lịch âm** (Lunar calendar) - Traditional Vietnamese calendar
- **Giỗ** - Death anniversary ceremony
- **Chạp** - Year-end ancestral worship
- **Tết** - Lunar New Year

See `docs/reference/glossary.md` for full terminology.

## Common Commands

```bash
# Frontend
cd frontend && npm run ios
cd frontend && npm run android
cd frontend && npm test

# Backend
cd backend && npm run start:dev
cd backend && npm run db:migrate
cd backend && npm test
```
