# Plan: Rebuild from Scratch — MVVM Architecture + Clean Git History

## Approach: Rebuild from Scratch (Option D) — Multi-Repo

### Project Structure

```
~/Development/
├── lunar-reminder-app/              ← Root repo (docs + agent) - EXISTING
│   ├── docs/                        # All documentation
│   ├── .claude/                     # Claude agent configs
│   ├── CLAUDE.md                    # Agent instructions
│   └── backupCode/                  # Backup of original code
│
├── lunar-reminder-frontend/         ← FE repo (React Native) - NEW
│   ├── src/
│   ├── package.json
│   ├── .github/workflows/
│   ├── .husky/
│   └── .git/
│
└── lunar-reminder-backend/          ← BE repo (NestJS) - NEW
    ├── src/
    ├── package.json
    ├── .github/workflows/
    ├── .husky/
    └── .git/
```

### Steps
1. **Backup** ✅ Done — code đã được backup vào `backupCode/`
2. **Create repos** — Tạo 2 GitHub repos mới: `lunar-reminder-frontend`, `lunar-reminder-backend`
3. **Init projects** — Setup từng repo với configs riêng
4. **Code lại từng commit** theo plan — clean, incremental git history
5. **Workflow diagram** → `docs/process/REBUILD_WORKFLOW.md`

### Lý do chọn Multi-Repo
- **Independence:** FE và BE có thể deploy độc lập
- **Team scalability:** Nhiều dev có thể làm việc song song
- **CI/CD riêng:** Mỗi repo có pipeline riêng, không ảnh hưởng nhau
- **Clean history:** Mỗi repo có git history chỉ liên quan đến layer đó

---

## MVVM Architecture (Frontend)

### Pattern
```
Model      → types/, services/, store/slices/  (data + API + state)
View       → *.tsx                             (pure JSX + styles import)
ViewModel  → useXxxViewModel.ts                (hooks: state, handlers, logic)
Styles     → *.styles.ts                       (StyleSheet.create)
```

### Screen Structure
```
screens/events/CreateEvent/
  ├── CreateEventScreen.tsx              # View — JSX only, calls ViewModel hook
  ├── useCreateEventViewModel.ts         # ViewModel — state, validation, handlers
  ├── CreateEventScreen.styles.ts        # Styles — StyleSheet.create
  └── index.ts                           # Barrel export
```

### Component Structure
```
components/events/EventCard/
  ├── EventCard.tsx                      # View (small components = View only)
  ├── EventCard.styles.ts               # Styles (tách nếu > 80 LOC styles)
  └── index.ts                          # Barrel
```

### Backend (NestJS — already MVC)
```
Model      → entities/, dto/              (TypeORM entities + validation DTOs)
View       → controllers/                 (HTTP endpoints + Swagger)
ViewModel  → services/                    (business logic)
```

---

## Git Commit Rules

### Quy tắc file — 600 LOC limit
| Loại file | LOC tối đa | Ghi chú |
|-----------|-----------|---------|
| Screen View (*.tsx) | **600 LOC** | Pure JSX + layout |
| Screen ViewModel (hook) | **400 LOC** | State + handlers + validation |
| Screen Styles | **300 LOC** | StyleSheet.create |
| Component | **400 LOC** | View + styles combined nếu nhỏ |
| Service / Hook / Utils | **400 LOC** | Nếu vượt → split theo chức năng |
| Entity / DTO / Config | **200 LOC** | Thường nhỏ |
| Test file | **600 LOC** | Cho phép nhiều test cases |

**Hard limit: 600 LOC/file.** Không file nào được vượt.

### Quy tắc commit

**Trước mỗi commit PHẢI verify:**
```bash
# FE repo
npm run lint          # ✅ No errors, no warnings
npx tsc --noEmit      # ✅ No TypeScript errors
npm run ios           # ✅ Build + run OK

# BE repo
npm run lint          # ✅ No errors, no warnings
npm run build         # ✅ Build OK
npm run start:dev     # ✅ Server starts OK
```

**Quy tắc:**
- **Max 5 files** per commit (không tính index.ts barrel exports)
- **Mỗi commit = 1 feature/screen/component** logic
- **Commit message:** Conventional Commits + scope
- **Mỗi commit phải build được** (không broken imports)
- **Test đi kèm feature** khi có thể (hoặc commit test riêng ngay sau)

**Checklist tham khảo:** `.github/merge_request_templates/Feature.md`

| Check | Mô tả |
|-------|-------|
| Lint | Passed lint check, no warnings |
| Build | Build success |
| Debug | Không để DebugLog, console.log trong code |
| Naming | Tên class, func, module đúng chức năng |
| Spelling | Không có lỗi chính tả |
| Method size | Method không quá 50 dòng |
| Nested loops | Không vòng lặp lồng nhau quá 3 lớp |
| Convention | Đúng coding convention (camelCase, PascalCase) |
| Switch/case | Đã break, default chính xác |
| Variables | Biến đã khởi tạo, tên rõ ràng |
| Array access | Check truy cập phần tử vượt quá mảng |
| If/else | Dễ hiểu, không phủ định kép |

---

## Implementation Plan: 9 Phases (Feature-based), 73 Commits

### Chiến lược: Full-Stack per Feature

Thay vì tách riêng BE rồi FE, mỗi phase gom **BE API + FE screens + Tests** cho cùng 1 feature.
Lợi ích:
- Làm xong 1 feature = dùng được end-to-end ngay
- Dễ test integration sớm
- Tránh disconnect giữa API và UI
- Review PR theo feature, không theo layer

### Tổng quan phases

```
Phase 0: Multi-Repo Setup + DevOps                    (12 commits: C00-C11, ~65 files, 12 docs)
Phase 1: Core Infrastructure — BE + FE Foundation     (8 commits: C13-C20)
Phase 2: Auth & Users — Full Stack                    (10 commits: C21-C30)
Phase 3: Events & Lunar Calendar — Full Stack         (12 commits: C31-C42)
Phase 4: Home & Calendar — FE                         (5 commits: C43-C47)
Phase 5: Groups — Full Stack                          (6 commits: C48-C53)
Phase 6: Notifications & Settings — Full Stack        (7 commits: C54-C60)
Phase 7: Share, Sync, Search & Export — Full Stack    (7 commits: C61-C67)
Phase 8: Migrations + Navigation + Final              (5 commits: C68-C72)
```

**Tổng: 72 commits, 9 phases** (across 3 repos: root, FE, BE)

---

## PHASE 0: Multi-Repo Setup + DevOps (12 commits)

> **Mục tiêu:** Tạo 2 repos mới (FE + BE), setup configs riêng cho từng repo, update docs ở root repo.
> **Lưu ý:** Phase 0 tạo repos mới, không giới hạn số files per commit.

### Repo Setup Overview

| Repo | GitHub URL | Local Path |
|------|-----------|------------|
| **Root (docs)** | `lunar-reminder-app` (existing) | `~/Development/lunar-reminder-app/` |
| **Frontend** | `lunar-reminder-frontend` (new) | `~/Development/lunar-reminder-app/lunar-reminder-frontend/` |
| **Backend** | `lunar-reminder-backend` (new) | `~/Development/lunar-reminder-app/lunar-reminder-backend/` |

---

### C00 — Create GitHub repos and init projects with proper CLI

**Thao tác:**

```bash
# 1. Create GitHub repos
gh repo create lunar-reminder-frontend --public --description "Lunar Reminder - React Native Mobile App"
gh repo create lunar-reminder-backend --public --description "Lunar Reminder - NestJS API Server"

# 2. Init FE với React Native CLI
cd ~/Development/lunar-reminder-app
npx react-native init LunarReminderFrontend --template react-native-template-typescript
mv LunarReminderFrontend lunar-reminder-frontend
cd lunar-reminder-frontend
git remote add origin git@github.com:YOUR_USERNAME/lunar-reminder-frontend.git

# 3. Verify FE chạy được
npm run ios  # hoặc npm run android
# ✅ App hiển thị màn hình welcome → OK

# 4. Init BE với NestJS CLI
cd ~/Development/lunar-reminder-app
npx @nestjs/cli new lunar-reminder-backend --package-manager npm --strict
cd lunar-reminder-backend
git remote add origin git@github.com:YOUR_USERNAME/lunar-reminder-backend.git

# 5. Verify BE chạy được
npm run start:dev
# ✅ http://localhost:3000 trả về "Hello World!" → OK

# 6. Commit init (mỗi repo)
cd ~/Development/lunar-reminder-app/lunar-reminder-frontend
git add -A && git commit -m "chore: init React Native project with TypeScript"
git push -u origin main

cd ~/Development/lunar-reminder-app/lunar-reminder-backend
git add -A && git commit -m "chore: init NestJS project with strict mode"
git push -u origin main
```

**Verify trước khi tiếp tục:**
- [ ] FE: `npm run ios` hoặc `npm run android` → app chạy OK
- [ ] BE: `npm run start:dev` → http://localhost:3000 OK
- [ ] Cả 2 repos đã push lên GitHub

---

### C01 — `chore(mobile): add dependencies and path aliases` (FE repo)

**Repo:** `lunar-reminder-frontend`

> **Note:** package.json và tsconfig.json đã có từ init. Chỉ cần thêm dependencies và path aliases.

| # | File | Mô tả |
|---|------|-------|
| 1 | `package.json` | Thêm deps: redux-toolkit, navigation 6, axios + devDeps: eslint, prettier, husky, lint-staged, commitlint |
| 2 | `tsconfig.json` | Thêm path aliases (@components, @screens, @services...) |
| 3 | `babel.config.js` | Thêm module-resolver cho path aliases |

**Verify trước commit:**
```bash
npm install
npm run ios  # hoặc android
# ✅ App vẫn chạy OK với deps mới
```

**Doc (root repo):** `docs/setup/FRONTEND_PACKAGE.md`

---

### C02 — `chore(api): add dependencies and path aliases` (BE repo)

**Repo:** `lunar-reminder-backend`

> **Note:** package.json và tsconfig.json đã có từ `nest new`. Chỉ cần thêm dependencies và path aliases.

| # | File | Mô tả |
|---|------|-------|
| 1 | `package.json` | Thêm deps: typeorm, pg, passport-jwt, swagger, bcrypt, @nestjs/schedule + devDeps: husky, lint-staged, commitlint |
| 2 | `tsconfig.json` | Thêm path aliases (@modules, @common, @config...) |

**Verify trước commit:**
```bash
npm install
npm run build
npm run start:dev
# ✅ http://localhost:3000 vẫn OK
```

**Doc (root repo):** `docs/setup/BACKEND_PACKAGE.md`

---

### C03 — `chore(mobile): add Prettier and EditorConfig` (FE repo)

**Repo:** `lunar-reminder-frontend`

> **Note:** ESLint config đã có từ init. Thêm Prettier + EditorConfig + customize ESLint.

| # | File | Mô tả |
|---|------|-------|
| 1 | `.prettierrc` | singleQuote, semi, printWidth 100, trailingComma all |
| 2 | `.prettierignore` | node_modules, ios, android, coverage, *.lock |
| 3 | `.eslintrc.js` | Extend existing + thêm import order, react-hooks rules |
| 4 | `.editorconfig` | indent_style space, indent_size 2, charset utf-8 |

**Verify:**
```bash
npm run lint
npx prettier --check "src/**/*.{ts,tsx}"
# ✅ No errors
```

**Doc (root repo):** `docs/setup/FRONTEND_LINTING.md`

---

### C04 — `chore(api): add EditorConfig and customize ESLint` (BE repo)

**Repo:** `lunar-reminder-backend`

> **Note:** ESLint + Prettier config đã có từ `nest new`. Chỉ cần thêm EditorConfig + customize.

| # | File | Mô tả |
|---|------|-------|
| 1 | `.prettierrc` | Customize: printWidth 100 |
| 2 | `.eslintrc.js` | Extend existing + thêm import order rules |
| 3 | `.editorconfig` | indent_style space, indent_size 2, charset utf-8 |

**Verify:**
```bash
npm run lint
npm run format
# ✅ No errors
```

**Doc (root repo):** `docs/setup/BACKEND_LINTING.md`

---

### C05 — `chore(mobile): add Metro, Jest, and Git hooks` (FE repo)

**Repo:** `lunar-reminder-frontend`

> **Note:** babel.config.js đã update ở C01. Metro và Jest config đã có từ init, chỉ cần customize.

| # | File | Mô tả |
|---|------|-------|
| 1 | `metro.config.js` | Customize Metro bundler config |
| 2 | `jest.config.js` | Customize Jest config (moduleNameMapper cho path aliases) |
| 3 | `commitlint.config.cjs` | Conventional commits + scopes: mobile, ui, navigation |
| 4 | `.husky/pre-commit` | Run lint-staged |
| 5 | `.husky/commit-msg` | Run commitlint |
| 6 | `.husky/pre-push` | Run typecheck + test |

**Verify:**
```bash
npx husky install
npm run lint && npm run test
# ✅ Hooks hoạt động
```

**Doc (root repo):** `docs/setup/FRONTEND_BUILD.md`

---

### C06 — `chore(api): add Git hooks` (BE repo)

**Repo:** `lunar-reminder-backend`

> **Note:** nest-cli.json và jest config đã có từ `nest new`. Chỉ cần thêm husky + commitlint.

| # | File | Mô tả |
|---|------|-------|
| 1 | `commitlint.config.cjs` | Conventional commits + scopes: api, auth, events, users |
| 2 | `.husky/pre-commit` | Run lint-staged |
| 3 | `.husky/commit-msg` | Run commitlint |
| 4 | `.husky/pre-push` | Run typecheck + test |

**Verify:**
```bash
npx husky install
npm run lint && npm run test
# ✅ Hooks hoạt động
```

**Docs (root repo):**
- `docs/setup/BACKEND_BUILD.md`
- `docs/setup/COMMIT_CONVENTION.md`

---

### C07 — `chore: add GitHub templates and CI workflow` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | Mô tả |
|---|------|-------|
| 1 | `.github/PULL_REQUEST_TEMPLATE.md` | PR template cho React Native |
| 2 | `.github/ISSUE_TEMPLATE/bug_report.md` | Bug report với device/OS/app version |
| 3 | `.github/ISSUE_TEMPLATE/feature_request.md` | Feature request template |
| 4 | `.github/ISSUE_TEMPLATE/config.yml` | Issue template chooser |
| 5 | `.github/workflows/ci.yml` | Lint + TypeCheck + Test + Build |
| 6 | `.github/workflows/deploy-staging.yml` | Deploy to TestFlight/Firebase |
| 7 | `.github/dependabot.yml` | Auto-update dependencies |
| 8 | `.github/CODEOWNERS` | Auto-assign reviewers |

**FE PR Template highlights:**
- MVVM pattern check
- StyleSheet (no inline styles)
- Accessibility labels
- Screenshots: Before/After

---

### C08 — `chore: add GitHub templates and CI workflow` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | Mô tả |
|---|------|-------|
| 1 | `.github/PULL_REQUEST_TEMPLATE.md` | PR template cho NestJS |
| 2 | `.github/ISSUE_TEMPLATE/bug_report.md` | Bug report với API endpoint |
| 3 | `.github/ISSUE_TEMPLATE/feature_request.md` | Feature request template |
| 4 | `.github/ISSUE_TEMPLATE/config.yml` | Issue template chooser |
| 5 | `.github/workflows/ci.yml` | Lint + TypeCheck + Test |
| 6 | `.github/workflows/deploy-staging.yml` | Deploy to staging server |
| 7 | `.github/workflows/deploy-production.yml` | Deploy to production (manual) |
| 8 | `.github/dependabot.yml` | Auto-update dependencies |
| 9 | `.github/CODEOWNERS` | Auto-assign reviewers |

**BE PR Template highlights:**
- DTOs validation
- Swagger decorators
- Unit tests coverage
- Database migration check

**Doc (root repo):** `docs/setup/GITHUB_TEMPLATES.md`

---

### C09 — `chore: add Docker and environment config` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | Mô tả |
|---|------|-------|
| 1 | `Dockerfile` | Multi-stage build node:20-alpine |
| 2 | `Dockerfile.dev` | Development Dockerfile with hot reload |
| 3 | `docker-compose.yml` | API + PostgreSQL + pgAdmin + Redis |
| 4 | `docker-compose.dev.yml` | Development override |
| 5 | `.env.example` | All env vars với placeholders |

**Doc (root repo):**
- `docs/setup/DOCKER_SETUP.md`
- `docs/setup/ENVIRONMENT_VARS.md`

---

### C10 — `chore: add environment config` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | Mô tả |
|---|------|-------|
| 1 | `.env.example` | API_URL, env-specific configs |
| 2 | `app.json` | Expo/RN app config |
| 3 | `index.js` | App entry point |

---

### C11 — `docs: update root repo with multi-repo guides` (Root repo)

**Repo:** `lunar-reminder-app`

| # | File | Mô tả |
|---|------|-------|
| 1 | `README.md` | Updated with multi-repo structure, links to FE/BE repos |
| 2 | `docs/setup/DEVELOPMENT_SETUP.md` | Full setup guide for all 3 repos |
| 3 | `docs/setup/TROUBLESHOOTING.md` | Common issues và solutions |
| 4 | `docs/process/REBUILD_WORKFLOW.md` | Updated workflow diagram |
| 5 | `docs/setup/index.md` | Setup docs hub |
| 6 | `docs/setup/CI_CD_WORKFLOW.md` | CI/CD flow cho cả FE + BE |

---

### Checkpoint Phase 0

Sau Phase 0 hoàn thành, verify:

**Frontend repo:**
- [ ] `cd lunar-reminder-frontend && npm install` works
- [ ] `npm run lint` passes
- [ ] Git hooks hoạt động (commit sai format → reject)
- [ ] GitHub PR template hiển thị đúng
- [ ] CI workflow chạy thành công

**Backend repo:**
- [ ] `cd lunar-reminder-backend && npm install` works
- [ ] `npm run lint` passes
- [ ] Git hooks hoạt động
- [ ] `docker-compose up` chạy được PostgreSQL + pgAdmin
- [ ] CI workflow chạy thành công

**Root repo:**
- [ ] Docs đầy đủ và links đúng
- [ ] Claude đọc được context từ .claude/

---

### Tổng kết Phase 0

| Commit | Repo | Chủ đề | Files |
|--------|------|--------|-------|
| C00 | - | Create GitHub repos | Git only |
| C01 | FE | package.json + tsconfig | 5 |
| C02 | BE | package.json + tsconfig | 6 |
| C03 | FE | Prettier + ESLint | 5 |
| C04 | BE | Prettier + ESLint | 5 |
| C05 | FE | Babel + Metro + Husky | 7 |
| C06 | BE | nest-cli + Husky | 6 |
| C07 | FE | GitHub templates + CI | 8 |
| C08 | BE | GitHub templates + CI | 9 |
| C09 | BE | Docker + env | 5 |
| C10 | FE | env + app config | 3 |
| C11 | Root | Docs update | 6 |
| **Total** | | | **~65 files** |

**Docs created (root repo):** 12 docs
- FRONTEND_PACKAGE.md, BACKEND_PACKAGE.md
- FRONTEND_LINTING.md, BACKEND_LINTING.md
- FRONTEND_BUILD.md, BACKEND_BUILD.md
- COMMIT_CONVENTION.md, GITHUB_TEMPLATES.md
- DOCKER_SETUP.md, ENVIRONMENT_VARS.md
- CI_CD_WORKFLOW.md, DEVELOPMENT_SETUP.md

---

## PHASE 1: Core Infrastructure — BE Bootstrap + FE Foundation (8 commits: C13-C20)

> **Mục tiêu:** Dựng xong skeleton cho cả BE và FE. Sau phase này có thể chạy được `npm run start:dev` (BE) và `npm run ios` (FE) với app trống.

### C13 — `feat(api): add app bootstrap with Swagger and global pipes` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/main.ts` | 67 | NestFactory, CORS, Swagger, ValidationPipe |
| 2 | `src/app.module.ts` | 63 | ConfigModule, TypeORM, ScheduleModule, all imports |

### C14 — `feat(api): add config files and common layer` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/config/database.config.ts` | 25 | registerAs('database') |
| 2 | `src/config/jwt.config.ts` | 20 | registerAs('jwt') |
| 3 | `src/config/onesignal.config.ts` | 15 | registerAs('onesignal') |
| 4 | `src/common/enums/index.ts` | 49 | EventType, CalendarType, NotificationStatus... |
| 5 | `src/common/decorators/current-user.decorator.ts` | 12 | createParamDecorator |

### C15 — `feat(api): add common guards, filters, and interceptors` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/common/guards/jwt-auth.guard.ts` | 20 | AuthGuard('jwt') |
| 2 | `src/common/filters/http-exception.filter.ts` | 100 | Global error formatting |
| 3 | `src/common/interceptors/transform.interceptor.ts` | 30 | Wrap response {success, data, timestamp} |

### C16 — `feat(mobile): add app entry point and theme constants` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/App.tsx` | 33 | Provider, PersistGate, NavigationContainer |
| 2 | `src/constants/theme.ts` | 120 | colors, spacing, typography, borderRadius |
| 3 | `src/constants/config.ts` | 80 | EVENT_TYPES, API_URL, APP_CONFIG |

### C17 — `feat(mobile): add type definitions (Model layer)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/types/models.ts` | 120 | Core interfaces: User, Event, Group, Notification |
| 2 | `src/types/dto.ts` | 80 | Request/Response DTOs |
| 3 | `src/types/index.ts` | 10 | Barrel re-exports |

> **MVVM:** Types split thành models (domain) + dto (API contracts)

### C18 — `feat(mobile): add Redux store with base slices (Model layer)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/store/index.ts` | 40 | configureStore + persistor |
| 2 | `src/store/slices/authSlice.ts` | 120 | auth state + async thunks |
| 3 | `src/store/slices/uiSlice.ts` | 40 | theme, onboarding |

### C19 — `feat(mobile): add common UI components (View layer)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/components/common/Text.tsx` | 80 | H1, H2, Body, Caption variants |
| 2 | `src/components/common/Input.tsx` | 100 | Label, error, icons |
| 3 | `src/components/common/Button.tsx` | 90 | Primary/secondary/outline + loading |
| 4 | `src/components/common/index.ts` | 8 | Barrel exports |

### C20 — `feat(mobile): add common UI components (card, icon, layout)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/components/common/Card.tsx` | 50 | Card wrapper with shadow |
| 2 | `src/components/common/Icon.tsx` | 60 | Icon component |
| 3 | `src/components/common/Loading.tsx` | 25 | Spinner |
| 4 | `src/components/common/SafeArea.tsx` | 30 | SafeAreaView wrapper |

---

## PHASE 2: Auth & Users — Full Stack (10 commits: C21-C30)

> **Mục tiêu:** BE auth API hoàn chỉnh + FE login/register/onboarding screens. Sau phase này user có thể đăng ký, đăng nhập, xem profile.

### C21 — `feat(api): add User entity and auth DTOs` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/users/entities/user.entity.ts` | 75 | User entity (UUID, email, phone, passwordHash) |
| 2 | `src/modules/auth/dto/register.dto.ts` | 40 | @IsEmail, @IsString, @MinLength |
| 3 | `src/modules/auth/dto/login.dto.ts` | 30 | email/phone + password |
| 4 | `src/modules/auth/dto/refresh-token.dto.ts` | 15 | @IsString refreshToken |

### C22 — `feat(api): add auth service with JWT token generation` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/auth/auth.service.ts` | 184 | register, login, refresh, logout |
| 2 | `src/modules/auth/strategies/jwt.strategy.ts` | 35 | PassportStrategy validate |

### C23 — `feat(api): add auth controller and users module` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/auth/auth.controller.ts` | 59 | POST register/login/refresh/logout |
| 2 | `src/modules/auth/auth.module.ts` | 35 | JwtModule, PassportModule |
| 3 | `src/modules/users/users.module.ts` | 20 | TypeOrmModule.forFeature |
| 4 | `src/modules/users/users.service.ts` | 140 | findByEmail, findByPhone, create, update |
| 5 | `src/modules/users/users.controller.ts` | 62 | GET profile, PUT profile |

### C24 — `feat(api): add user profile DTOs` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/users/dto/update-profile.dto.ts` | 25 | fullName, phone, timezone |
| 2 | `src/modules/users/dto/change-password.dto.ts` | 20 | currentPassword, newPassword |

### C25 — `feat(mobile): add API service and auth service (Model layer)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/services/api.ts` | 80 | Axios instance, interceptors, token refresh |
| 2 | `src/services/authService.ts` | 60 | login, register, refresh, logout |
| 3 | `src/services/index.ts` | 8 | Barrel exports |

### C26 — `feat(mobile): add LoginScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/auth/Login/LoginScreen.tsx` | 120 | **View** — JSX form layout |
| 2 | `src/screens/auth/Login/useLoginViewModel.ts` | 80 | **ViewModel** — form state, validation, submit handler |
| 3 | `src/screens/auth/Login/LoginScreen.styles.ts` | 50 | **Styles** — StyleSheet |
| 4 | `src/screens/auth/Login/index.ts` | 3 | Barrel |

### C27 — `feat(mobile): add RegisterScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/auth/Register/RegisterScreen.tsx` | 140 | **View** — registration form |
| 2 | `src/screens/auth/Register/useRegisterViewModel.ts` | 90 | **ViewModel** — validation, register handler |
| 3 | `src/screens/auth/Register/RegisterScreen.styles.ts` | 50 | **Styles** |
| 4 | `src/screens/auth/Register/index.ts` | 3 | Barrel |

### C28 — `feat(mobile): add OnboardingScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/auth/Onboarding/OnboardingScreen.tsx` | 140 | **View** — welcome slides |
| 2 | `src/screens/auth/Onboarding/useOnboardingViewModel.ts` | 60 | **ViewModel** — slide navigation state |
| 3 | `src/screens/auth/Onboarding/OnboardingScreen.styles.ts` | 50 | **Styles** |
| 4 | `src/screens/auth/Onboarding/index.ts` | 3 | Barrel |
| 5 | `src/screens/auth/index.ts` | 5 | Auth screens barrel |

### C29 — `feat(mobile): add AuthNavigator and navigation types` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/navigation/AuthNavigator.tsx` | 50 | Login → Register → Onboarding |
| 2 | `src/navigation/types.ts` | 80 | RootStack, HomeStack, all param lists |
| 3 | `src/navigation/index.ts` | 5 | Barrel exports |

### C30 — `test: add auth and users tests` (BE + FE repos)

**Repos:** `lunar-reminder-backend` + `lunar-reminder-frontend`

| # | File | Repo | LOC | Mô tả |
|---|------|------|-----|-------|
| 1 | `src/modules/auth/auth.service.spec.ts` | BE | 200 | register, login, refresh tests |
| 2 | `src/modules/auth/auth.controller.spec.ts` | BE | 150 | endpoint integration tests |
| 3 | `src/modules/users/users.service.spec.ts` | BE | 150 | CRUD tests |

> **Checkpoint Phase 2:** BE `POST /auth/register`, `POST /auth/login` hoạt động. FE Login/Register screens kết nối API. Có thể test flow đăng ký → đăng nhập end-to-end.

---

## PHASE 3: Events & Lunar Calendar — Full Stack (12 commits: C31-C42)

> **Mục tiêu:** Core feature — tạo/xem/sửa/xóa sự kiện âm lịch. BE events + lunar API + FE event screens. Sau phase này user có thể CRUD events.

### C31 — `feat(api): add Event entities` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/events/entities/event.entity.ts` | 108 | Event (lunar dates, solar date, notify...) |
| 2 | `src/modules/events/entities/event-group.entity.ts` | 30 | Junction: event_id + group_id |

### C32 — `feat(api): add event DTOs` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/events/dto/create-event.dto.ts` | 139 | All fields with class-validator |
| 2 | `src/modules/events/dto/update-event.dto.ts` | 30 | PartialType(CreateEventDto) |
| 3 | `src/modules/events/dto/find-events.dto.ts` | 40 | Pagination + filters |

### C33 — `feat(api): add lunar calendar service` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/lunar-calendar/lunar-calendar.service.ts` | 289 | convertToSolar, getYearInfo |
| 2 | `src/modules/lunar-calendar/lunar-calendar.controller.ts` | 35 | POST /lunar/convert |
| 3 | `src/modules/lunar-calendar/lunar-calendar.module.ts` | 15 | Module definition |
| 4 | `src/modules/lunar-calendar/dto/convert-to-solar.dto.ts` | 25 | day, month, year, isLeap |
| 5 | `src/modules/lunar-calendar/dto/year-info.dto.ts` | 15 | year param |

### C34 — `feat(api): add events service and controller` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/events/events.service.ts` | 204 | CRUD + lunar conversion + notify |
| 2 | `src/modules/events/events.controller.ts` | 83 | CRUD endpoints with Swagger |
| 3 | `src/modules/events/events.module.ts` | 30 | TypeOrmModule, LunarCalendar import |

### C35 — `feat(mobile): add event Model layer (service + slice)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/services/eventService.ts` | 60 | **Model** — CRUD API calls |
| 2 | `src/store/slices/eventSlice.ts` | 120 | **Model** — events state + async thunks |
| 3 | `src/utils/colorUtils.ts` | 40 | getEventColor, darken/lighten |
| 4 | `src/utils/index.ts` | 5 | Barrel |

### C36 — `feat(mobile): add EventCard component (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/components/events/EventCard/EventCard.tsx` | 150 | **View** — card layout, badge, dates |
| 2 | `src/components/events/EventCard/EventCard.styles.ts` | 80 | **Styles** |
| 3 | `src/components/events/EventCard/index.ts` | 3 | Barrel |
| 4 | `src/components/events/index.ts` | 3 | Events components barrel |

### C37 — `feat(mobile): add EventListScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/events/EventList/EventListScreen.tsx` | 120 | **View** — FlatList + filters + FAB |
| 2 | `src/screens/events/EventList/useEventListViewModel.ts` | 80 | **ViewModel** — filter state, pagination |
| 3 | `src/screens/events/EventList/EventListScreen.styles.ts` | 50 | **Styles** |
| 4 | `src/screens/events/EventList/index.ts` | 3 | Barrel |

### C38 — `feat(mobile): add CreateEventScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/events/CreateEvent/CreateEventScreen.tsx` | 200 | **View** — form fields layout |
| 2 | `src/screens/events/CreateEvent/useCreateEventViewModel.ts` | 180 | **ViewModel** — form state, validation, submit |
| 3 | `src/screens/events/CreateEvent/CreateEventScreen.styles.ts` | 100 | **Styles** |
| 4 | `src/screens/events/CreateEvent/index.ts` | 3 | Barrel |

> **MVVM split:** Original 486 LOC → View 200 + ViewModel 180 + Styles 100 = 480 LOC across 3 files

### C39 — `feat(mobile): add EditEventScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/events/EditEvent/EditEventScreen.tsx` | 200 | **View** — edit form layout |
| 2 | `src/screens/events/EditEvent/useEditEventViewModel.ts` | 190 | **ViewModel** — load event, form state, update |
| 3 | `src/screens/events/EditEvent/EditEventScreen.styles.ts` | 100 | **Styles** |
| 4 | `src/screens/events/EditEvent/index.ts` | 3 | Barrel |

> **MVVM split:** Original 498 LOC → View 200 + ViewModel 190 + Styles 100 = 490 LOC across 3 files

### C40 — `feat(mobile): add EventDetailScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/events/EventDetail/EventDetailScreen.tsx` | 180 | **View** — detail layout + action buttons |
| 2 | `src/screens/events/EventDetail/useEventDetailViewModel.ts` | 100 | **ViewModel** — load event, delete, share |
| 3 | `src/screens/events/EventDetail/EventDetailScreen.styles.ts` | 70 | **Styles** |
| 4 | `src/screens/events/EventDetail/index.ts` | 3 | Barrel |
| 5 | `src/screens/events/index.ts` | 6 | Events screens barrel |

### C41 — `feat(mobile): add date utils and lunar hooks` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/hooks/useLunarDate.ts` | 60 | Lunar conversion hook |
| 2 | `src/utils/dateUtils.ts` | 80 | formatDate, parseDate, countdown |
| 3 | `src/utils/validationUtils.ts` | 50 | Form validation helpers |

### C42 — `test: add events and lunar calendar tests` (BE + FE repos)

**Repos:** `lunar-reminder-backend` + `lunar-reminder-frontend`

| # | File | Repo | LOC | Mô tả |
|---|------|------|-----|-------|
| 1 | `src/modules/events/events.service.spec.ts` | BE | 250 | CRUD + ownership tests |
| 2 | `src/modules/lunar-calendar/lunar-calendar.service.spec.ts` | BE | 150 | Conversion accuracy tests |
| 3 | `src/screens/events/__tests__/useCreateEventViewModel.test.ts` | FE | 200 | ViewModel unit tests |

> **Checkpoint Phase 3:** BE `CRUD /events` + `POST /lunar/convert` hoạt động. FE EventList/Create/Edit/Detail screens hoàn chỉnh. Test flow: tạo event âm lịch → tự convert sang dương lịch → hiện trong danh sách.

---

## PHASE 4: Home & Calendar — FE (5 commits: C43-C47)

> **Mục tiêu:** Dashboard + Calendar view. Phase thuần FE vì dùng data từ Events API đã có ở Phase 3.

### C43 — `feat(mobile): add HomeScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/home/Home/HomeScreen.tsx` | 180 | **View** — dashboard layout |
| 2 | `src/screens/home/Home/useHomeViewModel.ts` | 100 | **ViewModel** — upcoming events, quick actions |
| 3 | `src/screens/home/Home/HomeScreen.styles.ts` | 60 | **Styles** |
| 4 | `src/screens/home/Home/index.ts` | 3 | Barrel |
| 5 | `src/screens/home/index.ts` | 3 | Barrel |

### C44 — `feat(mobile): add CalendarViewScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/calendar/CalendarView/CalendarViewScreen.tsx` | 200 | **View** — calendar grid layout |
| 2 | `src/screens/calendar/CalendarView/useCalendarViewModel.ts` | 120 | **ViewModel** — month nav, date selection, lunar calc |
| 3 | `src/screens/calendar/CalendarView/CalendarViewScreen.styles.ts` | 80 | **Styles** |
| 4 | `src/screens/calendar/CalendarView/index.ts` | 3 | Barrel |
| 5 | `src/screens/calendar/index.ts` | 3 | Barrel |

> **MVVM split:** Original 422 LOC → View 200 + ViewModel 120 + Styles 80 = 400 LOC across 3 files

### C45 — `feat(mobile): add AppNavigator with bottom tabs` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/navigation/AppNavigator.tsx` | 255 | Bottom tabs: Home, Events, Calendar, Groups, Settings |

### C46 — `feat(mobile): add useAuth hook and app-level ViewModel` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/hooks/useAuth.ts` | 50 | isLoggedIn, login, logout |
| 2 | `src/hooks/index.ts` | 10 | Barrel exports |

### C47 — `test(mobile): add home and calendar ViewModel tests` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/home/__tests__/useHomeViewModel.test.ts` | 120 | ViewModel unit tests |
| 2 | `src/screens/calendar/__tests__/useCalendarViewModel.test.ts` | 150 | Calendar logic tests |
| 3 | `src/utils/__tests__/dateUtils.test.ts` | 100 | Date utility tests |
| 4 | `src/screens/events/__tests__/useEventListViewModel.test.ts` | 150 | List ViewModel tests |

> **Checkpoint Phase 4:** Home dashboard hiển thị upcoming events. Calendar view cho phép navigate tháng, chọn ngày, xem lunar date. Bottom tab navigation hoạt động.

---

## PHASE 5: Groups — Full Stack (6 commits: C48-C53)

> **Mục tiêu:** BE groups API + FE group screens. Sau phase này user có thể tạo nhóm và gán events vào nhóm.

### C48 — `feat(api): add groups module` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/groups/entities/group.entity.ts` | 40 | Group (name, color, icon, sortOrder) |
| 2 | `src/modules/groups/dto/create-group.dto.ts` | 25 | name, color, icon |
| 3 | `src/modules/groups/dto/update-group.dto.ts` | 15 | PartialType |
| 4 | `src/modules/groups/groups.service.ts` | 60 | CRUD with ownership |
| 5 | `src/modules/groups/groups.controller.ts` | 65 | CRUD endpoints |

### C49 — `feat(api): add groups module definition + tests` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/groups/groups.module.ts` | 20 | Module |
| 2 | `src/modules/groups/groups.service.spec.ts` | 150 | CRUD + ownership tests |

### C50 — `feat(mobile): add group Model layer (service + slice)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/services/groupService.ts` | 50 | **Model** — CRUD API calls |
| 2 | `src/store/slices/groupSlice.ts` | 100 | **Model** — groups state + async thunks |
| 3 | `src/components/groups/GroupCard/GroupCard.tsx` | 80 | **View** — card with color, icon, count |
| 4 | `src/components/groups/GroupCard/index.ts` | 3 | Barrel |
| 5 | `src/components/groups/index.ts` | 3 | Barrel |

### C51 — `feat(mobile): add GroupListScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/groups/GroupList/GroupListScreen.tsx` | 180 | **View** — list + FAB + create dialog |
| 2 | `src/screens/groups/GroupList/useGroupListViewModel.ts` | 120 | **ViewModel** — CRUD handlers, dialog state |
| 3 | `src/screens/groups/GroupList/GroupListScreen.styles.ts` | 80 | **Styles** |
| 4 | `src/screens/groups/GroupList/index.ts` | 3 | Barrel |

> **MVVM split:** Original 429 LOC → View 180 + ViewModel 120 + Styles 80 = 380 LOC across 3 files

### C52 — `feat(mobile): add GroupDetailScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/groups/GroupDetail/GroupDetailScreen.tsx` | 150 | **View** — detail + events list |
| 2 | `src/screens/groups/GroupDetail/useGroupDetailViewModel.ts` | 80 | **ViewModel** — load group, events |
| 3 | `src/screens/groups/GroupDetail/GroupDetailScreen.styles.ts` | 50 | **Styles** |
| 4 | `src/screens/groups/GroupDetail/index.ts` | 3 | Barrel |
| 5 | `src/screens/groups/index.ts` | 5 | Groups screens barrel |

### C53 — `test(mobile): add group ViewModel tests` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/groups/__tests__/useGroupListViewModel.test.ts` | 150 | CRUD + dialog tests |

> **Checkpoint Phase 5:** BE `CRUD /groups` hoạt động. FE GroupList/Detail screens. User có thể tạo nhóm "Giỗ Ông Bà", assign events vào nhóm.

---

## PHASE 6: Notifications & Settings — Full Stack (7 commits: C54-C60)

> **Mục tiêu:** BE notification system + FE settings screens. Sau phase này user có thể cấu hình thông báo, xem profile, đổi mật khẩu.

### C54 — `feat(api): add notification entities and DTOs` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/notifications/entities/notification.entity.ts` | 50 | Notification entity |
| 2 | `src/modules/notifications/entities/device.entity.ts` | 40 | Device entity |
| 3 | `src/modules/notifications/dto/update-notification-settings.dto.ts` | 30 | Settings DTO |
| 4 | `src/modules/notifications/dto/send-test-notification.dto.ts` | 15 | Test send DTO |

### C55 — `feat(api): add notifications service and OneSignal provider` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/notifications/notifications.service.ts` | 176 | scheduleForEvent, cancel, send |
| 2 | `src/modules/notifications/providers/onesignal.provider.ts` | 93 | OneSignal API wrapper |
| 3 | `src/modules/notifications/schedulers/notification.scheduler.ts` | 131 | @Cron check & send |

### C56 — `feat(api): add notifications controller + module` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/notifications/notifications.controller.ts` | 45 | GET upcoming, POST test, PUT settings |
| 2 | `src/modules/notifications/notifications.module.ts` | 25 | Module |

### C57 — `feat(mobile): add notification Model layer + SettingsScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/store/slices/notificationSlice.ts` | 80 | **Model** — notification preferences |
| 2 | `src/services/notificationService.ts` | 50 | **Model** — API + OneSignal SDK |
| 3 | `src/screens/settings/Settings/SettingsScreen.tsx` | 180 | **View** — menu list |
| 4 | `src/screens/settings/Settings/useSettingsViewModel.ts` | 80 | **ViewModel** — logout, theme toggle |
| 5 | `src/screens/settings/Settings/SettingsScreen.styles.ts` | 60 | **Styles** |

### C58 — `feat(mobile): add ProfileScreen + AboutScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/settings/Profile/ProfileScreen.tsx` | 180 | **View** — edit profile form |
| 2 | `src/screens/settings/Profile/useProfileViewModel.ts` | 100 | **ViewModel** — form state, update handler |
| 3 | `src/screens/settings/Profile/ProfileScreen.styles.ts` | 60 | **Styles** |
| 4 | `src/screens/settings/Profile/index.ts` | 3 | Barrel |
| 5 | `src/screens/settings/About/AboutScreen.tsx` | 80 | **View** — version, links (small = View only) |

### C59 — `feat(mobile): add NotificationSettingsScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/settings/NotificationSettings/NotificationSettingsScreen.tsx` | 130 | **View** — time slots, frequency |
| 2 | `src/screens/settings/NotificationSettings/useNotificationSettingsViewModel.ts` | 80 | **ViewModel** — settings handlers |
| 3 | `src/screens/settings/NotificationSettings/NotificationSettingsScreen.styles.ts` | 40 | **Styles** |
| 4 | `src/screens/settings/NotificationSettings/index.ts` | 3 | Barrel |
| 5 | `src/screens/settings/index.ts` | 8 | Settings screens barrel |

### C60 — `test: add notifications and settings tests` (BE + FE repos)

**Repos:** `lunar-reminder-backend` + `lunar-reminder-frontend`

| # | File | Repo | LOC | Mô tả |
|---|------|------|-----|-------|
| 1 | `src/modules/notifications/notifications.service.spec.ts` | BE | 200 | Schedule/cancel tests |
| 2 | `src/screens/settings/__tests__/useSettingsViewModel.test.ts` | FE | 100 | Settings ViewModel tests |

> **Checkpoint Phase 6:** Notification scheduling hoạt động. Settings/Profile/NotificationSettings screens. User có thể bật/tắt notification, đổi password, xem about.

---

## PHASE 7: Share, Sync, Search & Export — Full Stack (7 commits: C61-C67)

> **Mục tiêu:** Feature chia sẻ events, sync offline, tìm kiếm, export calendar. Sau phase này app gần như hoàn chỉnh.

### C61 — `feat(api): add shares module` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/shares/entities/share.entity.ts` | 45 | Share entity (code, type, expires) |
| 2 | `src/modules/shares/entities/shared-event.entity.ts` | 30 | SharedEvent junction |
| 3 | `src/modules/shares/dto/create-share.dto.ts` | 25 | shareType, entityIds |
| 4 | `src/modules/shares/shares.service.ts` | 112 | Create link, accept share |
| 5 | `src/modules/shares/shares.controller.ts` | 50 | POST, GET :code, POST :code/accept |

### C62 — `feat(api): add sync and calendar export modules` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/shares/shares.module.ts` | 20 | Shares module |
| 2 | `src/modules/sync/entities/sync-log.entity.ts` | 35 | SyncLog entity |
| 3 | `src/modules/sync/dto/sync.dto.ts` | 30 | SyncRequest DTO |
| 4 | `src/modules/sync/sync.service.ts` | 61 | Delta sync logic |
| 5 | `src/modules/sync/sync.controller.ts` | 30 | POST /sync |

### C63 — `feat(api): add calendar export + sync module definitions` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/modules/sync/sync.module.ts` | 20 | Sync module |
| 2 | `src/modules/calendar-export/calendar-export.service.ts` | 50 | Export to Google/iCal |
| 3 | `src/modules/calendar-export/calendar-export.controller.ts` | 30 | POST export endpoints |
| 4 | `src/modules/calendar-export/calendar-export.module.ts` | 15 | Module |
| 5 | `src/modules/calendar-export/dto/export-to-google.dto.ts` | 20 | Export params |

### C64 — `feat(mobile): add SearchScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/search/Search/SearchScreen.tsx` | 150 | **View** — search input + results list |
| 2 | `src/screens/search/Search/useSearchViewModel.ts` | 100 | **ViewModel** — debounce, filter state |
| 3 | `src/screens/search/Search/SearchScreen.styles.ts` | 50 | **Styles** |
| 4 | `src/screens/search/Search/index.ts` | 3 | Barrel |
| 5 | `src/screens/search/index.ts` | 3 | Barrel |

### C65 — `feat(mobile): add ShareScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/share/Share/ShareScreen.tsx` | 180 | **View** — share link UI |
| 2 | `src/screens/share/Share/useShareViewModel.ts` | 120 | **ViewModel** — link generation, copy |
| 3 | `src/screens/share/Share/ShareScreen.styles.ts` | 60 | **Styles** |
| 4 | `src/screens/share/Share/index.ts` | 3 | Barrel |

### C66 — `feat(mobile): add AcceptShareScreen (MVVM)` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/share/AcceptShare/AcceptShareScreen.tsx` | 200 | **View** — preview + accept |
| 2 | `src/screens/share/AcceptShare/useAcceptShareViewModel.ts` | 130 | **ViewModel** — load share, accept handler |
| 3 | `src/screens/share/AcceptShare/AcceptShareScreen.styles.ts` | 70 | **Styles** |
| 4 | `src/screens/share/AcceptShare/index.ts` | 3 | Barrel |
| 5 | `src/screens/share/index.ts` | 5 | Share screens barrel |

### C67 — `test: add search, share, and sync tests` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/search/__tests__/useSearchViewModel.test.ts` | 120 | Debounce + filter tests |
| 2 | `src/screens/share/__tests__/useShareViewModel.test.ts` | 100 | Link generation tests |

> **Checkpoint Phase 7:** Share link hoạt động end-to-end. Search events. Sync module sẵn sàng. Calendar export endpoints.

---

## PHASE 8: Migrations + Navigation + Final (5 commits: C68-C72)

> **Mục tiêu:** Database migrations, seed data, root navigation, i18n, final cleanup.

### C68 — `feat(api): add database migrations` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/migrations/001-create-users.ts` | 40 | users table |
| 2 | `src/migrations/002-create-events.ts` | 60 | events + event_groups tables |
| 3 | `src/migrations/003-create-groups.ts` | 35 | groups table |
| 4 | `src/migrations/004-create-notifications.ts` | 45 | notifications + devices tables |
| 5 | `src/migrations/005-create-shares-sync.ts` | 50 | shares + shared_events + sync_logs |

### C69 — `feat(api): add seed data for Vietnamese holidays` (BE repo)

**Repo:** `lunar-reminder-backend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/seeds/seed.ts` | 60 | Main seed runner |
| 2 | `src/seeds/vietnamese-holidays.seed.ts` | 50 | 8 traditional holidays |

### C70 — `feat(mobile): add RootNavigator and i18n` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/navigation/RootNavigator.tsx` | 106 | Root with auth check + stacks |
| 2 | `src/i18n/index.ts` | 30 | Vietnamese/English setup |

### C71 — `test(mobile): add utility and hook tests` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/utils/__tests__/colorUtils.test.ts` | 80 | Color utility tests |
| 2 | `src/utils/__tests__/validationUtils.test.ts` | 100 | Validation tests |
| 3 | `src/hooks/__tests__/useAuth.test.ts` | 80 | Auth hook tests |
| 4 | `src/hooks/__tests__/useLunarDate.test.ts` | 80 | Lunar hook tests |

### C72 — `chore: final cleanup, barrel exports` (FE repo)

**Repo:** `lunar-reminder-frontend`

| # | File | LOC | Mô tả |
|---|------|-----|-------|
| 1 | `src/screens/index.ts` | 15 | Master barrel |
| 2 | `src/components/index.ts` | 10 | Master barrel |
| 3 | `src/services/index.ts` | 10 | Updated barrel |
| 4 | `src/screens/settings/Settings/index.ts` | 3 | Settings barrel |

> **Checkpoint Phase 8:** Full database schema via migrations. Vietnamese holidays seeded. Complete navigation flow. All tests passing. App ready for QA.

---

## Bảng tổng hợp

### Thống kê theo Phase

| Phase | Mô tả | Commits | Files | Docs | ~LOC |
|-------|--------|---------|-------|------|------|
| 0 | Multi-Repo Setup + DevOps | 12 (C00-C11) | 65 | 12 | ~2,500 |
| 1 | Core Infrastructure (BE + FE) | 8 (C13-C20) | 23 | - | ~1,335 |
| 2 | Auth & Users (Full Stack) | 10 (C21-C30) | 33 | - | ~2,300 |
| 3 | Events & Lunar (Full Stack) | 12 (C31-C42) | 39 | - | ~2,900 |
| 4 | Home & Calendar (FE) | 5 (C43-C47) | 14 | - | ~1,290 |
| 5 | Groups (Full Stack) | 6 (C48-C53) | 18 | - | ~900 |
| 6 | Notifications & Settings (Full Stack) | 7 (C54-C60) | 23 | - | ~1,780 |
| 7 | Share, Sync, Search (Full Stack) | 7 (C61-C67) | 24 | - | ~1,620 |
| 8 | Migrations + Nav + Final | 5 (C68-C72) | 16 | - | ~930 |
| **Total** | | **72** | **~255** | **12** | **~15,555** |

### MVVM File Breakdown (Frontend)

| Layer | Suffix | Role | LOC Target |
|-------|--------|------|------------|
| **View** | `Screen.tsx` | Pure JSX, imports ViewModel hook | ≤ 200 LOC |
| **ViewModel** | `useXxxViewModel.ts` | State, handlers, validation, business logic | ≤ 400 LOC |
| **Styles** | `Screen.styles.ts` | StyleSheet.create | ≤ 300 LOC |
| **Model** | `service.ts`, `slice.ts` | API calls, Redux state | ≤ 400 LOC |

> **Tại sao MVVM?** Mỗi file có 1 responsibility rõ ràng. View không chứa logic. ViewModel dễ unit test (pure functions/hooks). Styles tách riêng không mix với JSX.

### LOC Safety Check — Không file nào vượt 600

| Screen cũ | LOC cũ | → View | → ViewModel | → Styles | Total | Max file |
|-----------|--------|--------|-------------|----------|-------|----------|
| CreateEventScreen | 486 | 200 | 180 | 100 | 480 | **200** |
| EditEventScreen | 498 | 200 | 190 | 100 | 490 | **200** |
| CalendarViewScreen | 422 | 200 | 120 | 80 | 400 | **200** |
| GroupListScreen | 429 | 180 | 120 | 80 | 380 | **180** |
| ShareScreen | 352 | 180 | 120 | 60 | 360 | **180** |
| AcceptShareScreen | 390 | 200 | 130 | 70 | 400 | **200** |

---

## Dependency Graph (Feature-based)

```
Phase 0 (Backup + Init + DevOps)
  └── Phase 1 (Core: BE bootstrap + FE foundation)
       ├── Phase 2 (Auth & Users: BE auth API + FE auth screens)
       │    ├── Phase 3 (Events & Lunar: BE events API + FE event screens)
       │    │    ├── Phase 4 (Home & Calendar: FE dashboard + calendar)
       │    │    ├── Phase 5 (Groups: BE groups API + FE group screens)
       │    │    └── Phase 7 (Share/Sync/Search: BE + FE)
       │    └── Phase 6 (Notifications & Settings: BE notif + FE settings)
       └── Phase 8 (Migrations + Navigation + Final)
```

### So sánh với plan cũ (Layer-based)

| | Plan cũ (Layer-based) | Plan mới (Multi-Repo + Feature-based) |
|---|---|---|
| **Repos** | 1 monorepo | 3 repos (root, FE, BE) |
| **Phases** | 16 phases | 9 phases |
| **Commits** | ~65 | 72 |
| **Pattern** | BE all → FE all | BE+FE per feature |
| **Testable sớm nhất** | Phase 9 (sau ~27 commits) | Phase 2 (sau ~22 commits) |
| **End-to-end feature** | Phase 9+ | Phase 2+ |
| **Review scope** | Review API riêng, UI riêng | Review cả feature 1 lần |
| **CI/CD** | Shared pipeline | Independent pipelines |

---

## Rebuild Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│              REBUILD WORKFLOW (Multi-Repo + Feature-based)       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. BACKUP (already done)                                        │
│     Code backed up to backupCode/ folder                         │
│                                                                  │
│  2. CREATE REPOS                                                 │
│     gh repo create lunar-reminder-frontend                       │
│     gh repo create lunar-reminder-backend                        │
│                                                                  │
│  3. PHASE 0: SETUP (12 commits: C00-C11, ~65 files, 12 docs)     │
│                                                                  │
│     C00: Create GitHub repos                                     │
│                                                                  │
│     C01─C06: Frontend + Backend Setup (separate repos)           │
│     ┌─────────────────────┐  ┌─────────────────────┐            │
│     │  lunar-reminder-    │  │  lunar-reminder-    │            │
│     │     frontend        │  │     backend         │            │
│     ├─────────────────────┤  ├─────────────────────┤            │
│     │ C01: package.json   │  │ C02: package.json   │            │
│     │      tsconfig       │  │      tsconfig       │            │
│     │ C03: Prettier       │  │ C04: Prettier       │            │
│     │      ESLint         │  │      ESLint         │            │
│     │ C05: Babel, Metro   │  │ C06: nest-cli       │            │
│     │      Husky hooks    │  │      Husky hooks    │            │
│     │ C07: GitHub CI      │  │ C08: GitHub CI      │            │
│     │ C10: env + app.json │  │ C09: Docker, .env   │            │
│     └─────────────────────┘  └─────────────────────┘            │
│                                                                  │
│     C11: Root repo docs update (lunar-reminder-app)              │
│                                                                  │
│  4. REBUILD (Phase 1 → Phase 8) — commits to FE/BE repos         │
│                                                                  │
│     Phase 1: BE Core + FE Foundation ────────┐                   │
│     Phase 2: Auth (BE API → FE screens) ─────┤                   │
│     Phase 3: Events (BE API → FE screens) ───┤                   │
│     Phase 4: Home + Calendar (FE only) ──────┤                   │
│     Phase 5: Groups (BE API → FE screens) ───┤                   │
│     Phase 6: Notif + Settings (BE → FE) ─────┤                   │
│     Phase 7: Share/Sync/Search (BE → FE) ────┤                   │
│     Phase 8: Migrations + Nav + Final ───────┘                   │
│                                                                  │
│     Per Phase (commits go to respective repos):                  │
│     ┌──────────────┐  ┌──────────────┐  ┌──────────────┐        │
│     │   BE repo    │→ │   FE repo    │→ │    Tests     │        │
│     │ (NestJS API) │  │ (RN MVVM)    │  │ (both repos) │        │
│     └──────────────┘  └──────────────┘  └──────────────┘        │
│                                                                  │
│  5. VERIFY (sau mỗi phase — run in each repo)                    │
│     cd lunar-reminder-backend && npm run lint && npm test        │
│     cd lunar-reminder-frontend && npm run lint && npm test       │
│     → Slack: "Phase X completed ✓"                               │
│                                                                  │
│  6. FINAL VERIFY (Phase 8)                                       │
│     BE: docker-compose up → curl /api/health                     │
│     FE: npm run ios / npm run android                            │
│     Full test suite with coverage (both repos)                   │
│     → Slack: "Rebuild completed! Ready for QA"                   │
│                                                                  │
│  7. DONE                                                         │
│     Clean git history: 73 atomic commits across 2 repos          │
│     All files ≤ 600 LOC                                          │
│     MVVM architecture enforced (FE)                              │
│     MVC architecture (BE)                                        │
│     Tests for every module                                       │
│     Independent CI/CD pipelines                                  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Slack Integration Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    SLACK INTEGRATION FLOW                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  CLAUDE → SLACK (Auto-report)                                    │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ 1. Task Started:  "🚀 Starting: [task description]"       │   │
│  │ 2. Commit Made:   "✅ Committed: [commit message]"        │   │
│  │ 3. Phase Done:    "🎉 Phase X completed (Y commits)"      │   │
│  │ 4. Blocker:       "🚨 Blocked: [issue description]"       │   │
│  │ 5. PR Created:    "📝 PR ready: [link]"                   │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  SLACK → CLAUDE (User feedback)                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ 1. "continue" → Claude continues with next task           │   │
│  │ 2. "fix: [issue]" → Claude fixes the issue                │   │
│  │ 3. "pause" → Claude pauses and waits                      │   │
│  │ 4. "review" → Claude runs self-review                     │   │
│  │ 5. "status" → Claude reports current status               │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  CI/CD → SLACK (Auto-notify)                                     │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ 1. CI Pass:  "✅ CI passed for [branch]"                  │   │
│  │ 2. CI Fail:  "❌ CI failed: [error summary]"              │   │
│  │ 3. Deploy:   "🚀 Deployed to [env]: [version]"            │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### MVVM Screen Pattern (mỗi screen)

```
screens/[feature]/[ScreenName]/
  ├── [ScreenName]Screen.tsx           # VIEW
  │   - Import useXxxViewModel hook
  │   - Pure JSX, no business logic
  │   - Destructure ViewModel return
  │
  ├── use[ScreenName]ViewModel.ts      # VIEWMODEL
  │   - useState, useEffect, useCallback
  │   - Form validation, handlers
  │   - Redux dispatch, API calls
  │   - Returns: { state, handlers }
  │
  ├── [ScreenName]Screen.styles.ts     # STYLES
  │   - StyleSheet.create({...})
  │   - Exported as `styles`
  │
  └── index.ts                         # BARREL
      - export { default } from './[ScreenName]Screen'
```

---

## Verification Plan

Sau mỗi Phase, chạy trong từng repo:

```bash
# Backend (lunar-reminder-backend)
cd ~/Development/lunar-reminder-backend
npx tsc --noEmit && npm run lint && npm test

# Frontend (lunar-reminder-frontend)
cd ~/Development/lunar-reminder-frontend
npx tsc --noEmit && npm run lint && npm test

# LOC check (no file > 600 lines) — run in each repo
find src -name "*.ts" -o -name "*.tsx" | xargs wc -l | sort -rn | head -20
```

Sau Phase 8 (final):
```bash
# Backend — Full integration
cd ~/Development/lunar-reminder-backend
docker-compose up -d
curl http://localhost:3000/api/health
npm test -- --coverage

# Frontend — Full test suite
cd ~/Development/lunar-reminder-frontend
npm test -- --coverage
```

---

## Related Documentation

- [SLACK_INTEGRATION.md](../setup/SLACK_INTEGRATION.md) - Hướng dẫn setup Slack webhook
- [REBUILD_WORKFLOW.md](./REBUILD_WORKFLOW.md) - Workflow diagram chi tiết
- [COMMIT_CONVENTION.md](../setup/COMMIT_CONVENTION.md) - Conventional commits format
