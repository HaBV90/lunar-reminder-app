# Project Lifecycle Checklist

**Checklist toàn diện từ ý tưởng đến hoàn thành dự án**

> Tài liệu này được tạo dựa trên kinh nghiệm thực tế từ dự án Lunar Reminder App. Mỗi phase bao gồm mục đích, checklist actionable, deliverables, và bài học rút ra.

---

**Version:** 1.0
**Ngày tạo:** 2026-02-04
**Tác giả:** Ha (PM/Tech Lead) + Claude (AI Assistant)
**Dự án tham chiếu:** Lunar Reminder App

---

## Mục lục

1. [Phase 1: Ý tưởng & Nghiên cứu](#phase-1-ý-tưởng--nghiên-cứu)
2. [Phase 2: Lập kế hoạch](#phase-2-lập-kế-hoạch)
3. [Phase 3: Thiết kế hệ thống](#phase-3-thiết-kế-hệ-thống)
4. [Phase 4: Thiết lập môi trường](#phase-4-thiết-lập-môi-trường)
5. [Phase 5: Phát triển](#phase-5-phát-triển)
6. [Phase 6: Kiểm thử](#phase-6-kiểm-thử)
7. [Phase 7: Tích hợp & Build](#phase-7-tích-hợp--build)
8. [Phase 8: Triển khai](#phase-8-triển-khai)
9. [Phase 9: Launch & Validation](#phase-9-launch--validation)
10. [Phase 10: Bảo trì & Cải tiến](#phase-10-bảo-trì--cải-tiến)
11. [Phụ lục A: AI-Assisted Development Checklist](#phụ-lục-a-ai-assisted-development-checklist)
12. [Phụ lục B: Template Files cho mọi dự án](#phụ-lục-b-template-files-cho-mọi-dự-án)
13. [Phụ lục C: Common Mistakes - 10 bài học](#phụ-lục-c-common-mistakes---10-bài-học)
14. [Phụ lục D: Sequence Diagram - Thứ tự lý tưởng](#phụ-lục-d-sequence-diagram---thứ-tự-lý-tưởng)

---

## Phase 1: Ý tưởng & Nghiên cứu

### Mục đích
Xác định rõ vấn đề cần giải quyết, đối tượng người dùng, và tính khả thi trước khi bắt đầu bất kỳ công việc kỹ thuật nào.

### Checklist

#### 1.1 Problem Definition
- [ ] Xác định vấn đề cụ thể cần giải quyết (viết thành 1-3 câu rõ ràng)
- [ ] Trả lời được: "Ai gặp vấn đề này?" (target users)
- [ ] Trả lời được: "Họ đang giải quyết như thế nào hiện tại?" (existing solutions)
- [ ] Trả lời được: "Tại sao giải pháp hiện tại chưa đủ tốt?" (pain points)

#### 1.2 Market Research
- [ ] Khảo sát các ứng dụng/giải pháp tương tự trên thị trường
- [ ] Liệt kê điểm mạnh/yếu của competitors (tối thiểu 3 competitors)
- [ ] Xác định unique selling points (USP) của sản phẩm mình
- [ ] Kiểm tra nhu cầu thực tế (survey, phỏng vấn, hoặc data từ forums/reviews)

#### 1.3 Feasibility Study
- [ ] Đánh giá technical feasibility: công nghệ nào cần dùng, có sẵn không?
- [ ] Đánh giá resource feasibility: team có đủ kỹ năng không? Cần bao lâu?
- [ ] Đánh giá financial feasibility: chi phí phát triển, hosting, maintenance
- [ ] Xác định risks chính và cách mitigation
- [ ] Đánh giá legal/compliance: privacy (GDPR), app store guidelines, licenses

#### 1.4 User Personas
- [ ] Tạo tối thiểu 2-3 user personas với: tên, tuổi, nghề nghiệp, goals, pain points
- [ ] Xác định primary persona (người dùng chính, thiết kế ưu tiên cho họ)
- [ ] Viết user stories cơ bản cho mỗi persona

### Deliverables
- [ ] Problem Statement document (1-2 trang)
- [ ] Competitor Analysis (bảng so sánh)
- [ ] User Personas (2-3 personas)
- [ ] Feasibility Assessment (go/no-go decision)

### Bài học từ Lunar Reminder App
> **Làm tốt:** Xác định rõ 3 pain points (chuyển đổi lịch âm-dương, nhớ ngày giỗ, chuẩn bị lễ).
> Tạo 3 personas cụ thể (Minh - trẻ xa nhà, Cô Lan - quản lý gia đình, Bé An - học sinh).
>
> **Chưa tốt:** Chưa khảo sát thực tế người dùng. Chưa test idea với người thân trước.
> Competitor analysis chỉ dựa trên kinh nghiệm cá nhân, chưa download và dùng thử từng app.

---

## Phase 2: Lập kế hoạch

### Mục đích
Biến ý tưởng thành kế hoạch thực thi cụ thể với scope, timeline, và resources rõ ràng.

### Checklist

#### 2.1 Scope Definition
- [ ] Xác định MVP scope: features nào PHẢI có cho v1.0
- [ ] Liệt kê "Out of Scope" rõ ràng (quan trọng không kém in-scope)
- [ ] Phân loại features thành: Must-have / Should-have / Nice-to-have (MoSCoW)
- [ ] Ước lượng effort cho mỗi feature (S/M/L/XL)
- [ ] Xác nhận scope với stakeholders (viết thành văn bản)

#### 2.2 Roadmap & Timeline
- [ ] Tạo high-level roadmap (milestone-based, không chi tiết ngày)
- [ ] Chia dự án thành phases/sprints rõ ràng
- [ ] Xác định dependencies giữa các tasks
- [ ] Thêm buffer time (tối thiểu +30% so với estimate ban đầu)
- [ ] Xác định milestones có thể demo/review

#### 2.3 Task Breakdown
- [ ] Chia mỗi feature thành tasks nhỏ (mỗi task < 1 ngày effort)
- [ ] Gắn priority cho mỗi task
- [ ] Xác định thứ tự thực hiện (critical path)
- [ ] Assign tasks cho team members (nếu có team)

#### 2.4 Tool Setup
- [ ] Chọn và setup project management tool (Jira, Linear, GitHub Projects...)
- [ ] Tạo board/backlog với tất cả tasks
- [ ] Setup communication channels (Slack, Discord, email groups)
- [ ] Setup documentation tool (Notion, Confluence, hoặc Git-based)
- [ ] Setup design tool (Figma, Sketch) nếu có designer

### Deliverables
- [ ] Scope document (approved by stakeholders)
- [ ] Project roadmap (visual timeline)
- [ ] Task breakdown (trong project management tool, KHÔNG phải chỉ trong docs)
- [ ] Team agreements (working hours, communication, review process)

### Bài học từ Lunar Reminder App
> **Làm tốt:** Scope MVP rõ ràng, phân loại features theo MoSCoW.
> Out-of-scope list chi tiết (8 categories).
>
> **Chưa tốt:** Timeline quá aggressive (7 ngày cho MVP hoàn chỉnh - không thực tế).
> Task breakdown chỉ viết trong markdown, chưa setup Jira/Linear thực tế.
> Chưa estimate effort cho từng task. Chưa xác định critical path.
> **Bài học lớn:** Planning docs dù chi tiết nhưng nếu không setup tool tracking thực tế thì vô nghĩa.

---

## Phase 3: Thiết kế hệ thống

### Mục đích
Thiết kế kiến trúc, database, API, và UI trước khi code. Giải quyết các quyết định kỹ thuật lớn TRƯỚC KHI viết code.

### Checklist

#### 3.1 Product Requirements Document (PRD)
- [ ] Viết PRD chi tiết: features, user stories, acceptance criteria
- [ ] Review PRD với stakeholders và team
- [ ] Approve PRD chính thức (có sign-off)
- [ ] Baseline version (v1.0) - mọi thay đổi sau đây phải change request

#### 3.2 Technical Specification
- [ ] Chọn tech stack với justification (viết ADR cho mỗi quyết định lớn)
- [ ] Thiết kế system architecture (diagram)
- [ ] Xác định communication patterns (REST, GraphQL, WebSocket)
- [ ] Thiết kế authentication & authorization flow
- [ ] Thiết kế error handling strategy
- [ ] Thiết kế caching strategy
- [ ] Thiết kế logging & monitoring strategy
- [ ] **QUAN TRỌNG:** Giải quyết TẤT CẢ pending decisions TRƯỚC KHI code
- [ ] Review tech spec với senior engineers

#### 3.3 Architecture Decision Records (ADRs)
- [ ] Viết ADR cho mỗi quyết định kiến trúc quan trọng
- [ ] Mỗi ADR gồm: Context, Decision, Consequences, Alternatives Considered
- [ ] Status rõ ràng: Proposed → Accepted → Deprecated/Superseded
- [ ] **KHÔNG còn ADR nào ở status "Pending" khi bắt đầu code**

#### 3.4 Data Model
- [ ] Thiết kế database schema (ERD diagram)
- [ ] Xác định relationships giữa các entities
- [ ] Xác định indexes cần thiết
- [ ] Viết migration scripts mẫu
- [ ] Review data model với team
- [ ] Xem xét data growth và scalability

#### 3.5 API Design
- [ ] Thiết kế API endpoints (REST conventions hoặc GraphQL schema)
- [ ] Viết API documentation (OpenAPI/Swagger hoặc tương đương)
- [ ] Xác định request/response formats
- [ ] Xác định error response formats (consistent)
- [ ] Xác định versioning strategy (nếu cần)
- [ ] Review API design với frontend + backend team

#### 3.6 UI/UX Design
- [ ] Tạo design system: colors, typography, spacing, icons
- [ ] Tạo wireframes cho tất cả screens
- [ ] Tạo high-fidelity mockups (nếu có designer)
- [ ] Xác định navigation flow
- [ ] Xác định component library
- [ ] Accessibility requirements (WCAG level)
- [ ] Review design với stakeholders

### Deliverables
- [ ] PRD (approved, baselined)
- [ ] Technical Specification
- [ ] ADRs (tất cả ở status Accepted hoặc Rejected, KHÔNG Pending)
- [ ] Data Model / ERD
- [ ] API Specification
- [ ] UI/UX Designs / Wireframes
- [ ] Glossary (nếu domain-specific)

### Bài học từ Lunar Reminder App
> **Làm tốt:** Documentation rất chi tiết (PRD 82KB, Tech Spec 105KB, UI/UX 45KB).
> 12 ADRs được viết cho các quyết định kiến trúc. Glossary cho Vietnamese terms.
>
> **Chưa tốt:** 2 ADR quan trọng (Calendar Library - AD-011, Lunar Algorithm - AD-012) vẫn ở
> status "Pending" khi đã bắt đầu code. Điều này dẫn đến việc phải thay đổi approach giữa chừng.
>
> **Bài học lớn:** "Pending decisions" là tech debt từ ngày đầu. Giải quyết HẾT trước khi code.
> Documentation chi tiết chỉ có giá trị khi được **sử dụng**, không phải chỉ được **viết**.

---

## Phase 4: Thiết lập môi trường

### Mục đích
Setup infrastructure phát triển: Git repo, CI/CD, dev environment, project scaffolding. **Đây là bước BẮT BUỘC phải hoàn thành trước khi viết business logic.**

### Checklist

#### 4.1 Git Repository Setup
- [ ] **FIRST COMMIT:** Tạo repo, init, và commit ngay với `.gitignore` + `README.md`
- [ ] Setup branch protection rules trên main/develop
- [ ] Tạo `.gitignore` đầy đủ cho tech stack
- [ ] Tạo PR template (`.github/PULL_REQUEST_TEMPLATE.md`)
- [ ] Tạo Issue template (`.github/ISSUE_TEMPLATE.md`)
- [ ] Setup branch naming convention (trong team docs)
- [ ] Commit message format (Conventional Commits)
- [ ] **CRITICAL:** Commit liên tục từ đầu, KHÔNG đợi "xong hết rồi commit một lần"

#### 4.2 CI/CD Pipeline
- [ ] Setup CI pipeline: lint → test → build (cho mỗi push/PR)
- [ ] Frontend CI: TypeScript check, ESLint, unit tests, build
- [ ] Backend CI: TypeScript check, ESLint, unit tests, build
- [ ] Setup CD pipeline cho staging (auto-deploy on merge to develop)
- [ ] Setup CD pipeline cho production (manual trigger hoặc tag-based)
- [ ] Configure status checks (CI phải pass trước khi merge PR)
- [ ] Setup secrets management (GitHub Secrets, env vars)

#### 4.3 Development Environment
- [ ] Viết setup guide chi tiết (README hoặc docs/guides/)
- [ ] Setup Node.js version (`.nvmrc` hoặc `.node-version`)
- [ ] Setup package manager (npm/yarn/pnpm - chọn 1, commit lockfile)
- [ ] Setup ESLint + Prettier với config files
- [ ] Setup pre-commit hooks (Husky + lint-staged)
- [ ] Setup TypeScript (`tsconfig.json` cho mỗi package)
- [ ] Setup path aliases (nếu dùng)
- [ ] Setup environment variables (`.env.example` file)
- [ ] Verify: clone fresh → follow guide → app chạy được

#### 4.4 Project Scaffolding
- [ ] Initialize frontend project (React Native, Next.js, etc.)
- [ ] Initialize backend project (NestJS, Express, etc.)
- [ ] Setup folder structure theo convention đã định
- [ ] Tạo placeholder files cho main modules
- [ ] Setup database connection (dev environment)
- [ ] Run initial migration
- [ ] Verify: `npm start` works cho cả frontend và backend

#### 4.5 Third-party Services
- [ ] Setup database (local dev + staging/prod)
- [ ] Setup authentication provider (nếu dùng external)
- [ ] Setup push notification service (OneSignal, FCM)
- [ ] Setup error tracking (Sentry)
- [ ] Setup analytics (nếu cần cho MVP)
- [ ] Setup email service (nếu cần)
- [ ] Document tất cả API keys và access credentials (secure storage)

### Deliverables
- [ ] Git repo với first commit + branch protection
- [ ] CI/CD pipeline chạy thành công
- [ ] Dev environment setup guide (tested by another person)
- [ ] Scaffolded project (both frontend and backend runnable)
- [ ] `.env.example` với tất cả required variables
- [ ] Pre-commit hooks configured

### Bài học từ Lunar Reminder App
> **Chưa tốt (nghiêm trọng):**
> - Sau 3 ngày phát triển, chưa có commit nào. Tất cả files vẫn ở status `??` (untracked).
> - Chưa tạo branch nào dù đã viết gitflow rules rất chi tiết.
> - CI/CD chưa cấu hình dù đã có GitHub Actions template files.
> - Pre-commit hooks (Husky) chưa setup.
> - `.env.example` chưa tạo.
>
> **Bài học lớn:** Setup git + CI/CD + first commit PHẢI là việc ĐẦU TIÊN, trước cả viết code.
> Viết rules mà không thực thi = không có rules. "Infrastructure as code" phải được commit.
> **Rule bất di bất dịch: Code mà không commit = code không tồn tại.**

---

## Phase 5: Phát triển

### Mục đích
Implement features theo kế hoạch, tuân thủ coding standards, và maintain code quality thông qua reviews.

### Checklist

#### 5.1 Sprint/Iteration Workflow
- [ ] Sprint planning: chọn tasks từ backlog cho sprint (1-2 tuần)
- [ ] Daily standup (dù chỉ 1 người cũng nên ghi daily log)
- [ ] Sprint review: demo completed features
- [ ] Sprint retrospective: what went well / what to improve

#### 5.2 Per-Feature Workflow (cho MỖI feature/task)
- [ ] Tạo branch mới từ main/develop: `feature/TASK-ID-description`
- [ ] Implement code (mỗi commit < 300 LOC thay đổi)
- [ ] Viết unit tests cho logic mới
- [ ] Chạy lint + format: `npm run lint -- --fix`
- [ ] Chạy tests: `npm test`
- [ ] Self-review code (đọc lại diff trước khi commit)
- [ ] Commit với Conventional Commits message
- [ ] Push branch lên remote
- [ ] Tạo Pull Request (draft → ready for review)
- [ ] Request code review
- [ ] Address review comments
- [ ] **Chờ approval** → Merge (squash hoặc rebase)
- [ ] Delete feature branch sau merge
- [ ] Update task tracker (Jira/Linear: In Progress → Done)

#### 5.3 Coding Standards Enforcement
- [ ] TypeScript strict mode: no `any`, explicit return types
- [ ] Functional components + hooks (React/RN)
- [ ] PascalCase: Components, Types, Interfaces
- [ ] camelCase: variables, functions
- [ ] kebab-case: file names (components excepted)
- [ ] Absolute imports với path aliases
- [ ] Import order: react → third-party → internal
- [ ] Error handling: try/catch với meaningful messages
- [ ] No inline styles (dùng Tailwind/NativeWind)
- [ ] Accessibility: `accessibilityLabel` cho interactive elements
- [ ] No `console.log` in production code

#### 5.4 Code Review Standards
- [ ] Reviewer checks: readability, correctness, performance, security
- [ ] Review response time: tối đa 24 giờ
- [ ] Tối thiểu 1 approval trước khi merge
- [ ] CI phải pass trước khi merge
- [ ] Không merge code của chính mình (nếu có team)

#### 5.5 Documentation During Development
- [ ] Update API docs khi thêm/sửa endpoints
- [ ] Update data model docs khi thay đổi schema
- [ ] Thêm inline comments cho logic phức tạp
- [ ] Update README nếu setup steps thay đổi
- [ ] Update CHANGELOG cho user-facing changes

### Deliverables
- [ ] Working features (merged to main/develop)
- [ ] Unit tests cho mỗi feature
- [ ] Updated documentation
- [ ] Clean git history (meaningful commits)

### Bài học từ Lunar Reminder App
> **Làm tốt:** Code structure tốt, path aliases, TypeScript strict mode.
> 160 unit tests cho utility functions. Coding standards được tuân thủ tốt trong code.
>
> **Chưa tốt (nghiêm trọng):**
> - KHÔNG tạo branch nào. Tất cả code trên main.
> - KHÔNG có commit nào. Không có PR/MR nào. Không có code review.
> - Mất toàn bộ ý nghĩa của git workflow đã viết chi tiết trong CLAUDE.md.
> - Code nhiều features cùng lúc mà chưa commit feature nào.
>
> **Bài học lớn:** Workflow chỉ giá trị khi THỰC HIỆN. Viết 10 trang git rules
> mà không commit lần nào = lãng phí thời gian viết rules.
> **Rule: Commit TRƯỚC khi viết feature tiếp theo. Không có ngoại lệ.**

---

## Phase 6: Kiểm thử

### Mục đích
Đảm bảo chất lượng phần mềm qua nhiều tầng testing: từ unit test đến acceptance test.

### Checklist

#### 6.1 Unit Testing
- [ ] Test coverage cho business logic: > 80%
- [ ] Test coverage cho utility functions: > 90%
- [ ] Mock external dependencies (API calls, database)
- [ ] Test edge cases: null, undefined, empty strings, max values
- [ ] Test error cases: invalid input, network errors
- [ ] Tests chạy nhanh (toàn bộ suite < 30 giây)
- [ ] Tests trong CI pipeline (fail = block merge)

#### 6.2 Integration Testing
- [ ] API endpoint tests (request → response)
- [ ] Database integration tests (CRUD operations)
- [ ] Authentication flow tests (login → token → protected route)
- [ ] Third-party service integration tests (mock in CI, real in staging)

#### 6.3 End-to-End (E2E) Testing
- [ ] Critical user flows (signup, create event, receive notification)
- [ ] Cross-platform testing: iOS + Android (React Native)
- [ ] Happy path + key error paths
- [ ] E2E framework setup (Detox cho React Native, hoặc Maestro)

#### 6.4 Security Testing
- [ ] Input validation: SQL injection, XSS
- [ ] Authentication: brute force protection, token expiry
- [ ] Authorization: role-based access, resource ownership
- [ ] Sensitive data: encryption at rest + in transit
- [ ] Dependency audit: `npm audit` → fix critical/high vulnerabilities
- [ ] OWASP Top 10 checklist

#### 6.5 Performance Testing
- [ ] API response time: < 200ms cho common operations
- [ ] App launch time: < 3 giây (React Native)
- [ ] List rendering performance (1000+ items)
- [ ] Memory usage profiling (no leaks)
- [ ] Bundle size monitoring
- [ ] Database query performance (no N+1 queries)

#### 6.6 Accessibility Testing
- [ ] Screen reader compatibility (VoiceOver iOS, TalkBack Android)
- [ ] Touch targets: minimum 44x44pt
- [ ] Color contrast: WCAG AA ratio
- [ ] Font scaling support
- [ ] Navigation by keyboard/switch

#### 6.7 Manual/Exploratory Testing
- [ ] Tạo test plan cho mỗi feature
- [ ] Test trên thiết bị thật (không chỉ simulator)
- [ ] Test offline mode
- [ ] Test với dữ liệu thực tế (Vietnamese text, dấu, tên dài)
- [ ] Test edge cases UI (text overflow, rotation, notch)

### Deliverables
- [ ] Test coverage report (>80% overall)
- [ ] Integration test results
- [ ] Security audit report
- [ ] Performance benchmark results
- [ ] Bug list (prioritized)
- [ ] Testing plan document

### Bài học từ Lunar Reminder App
> **Làm tốt:** 160 unit tests cho utility functions với coverage 97%+.
> Jest config đúng chuẩn, test files organized tốt.
>
> **Chưa tốt:**
> - Chỉ có unit tests cho utilities (dateUtils, validationUtils, colorUtils).
> - Chưa có tests cho hooks, components, screens, hoặc API.
> - Chưa có integration tests hoặc E2E tests.
> - Chưa test trên thiết bị thật.
> - Security testing chưa thực hiện.
>
> **Bài học:** Unit tests cho utils là bước đầu tốt, nhưng chỉ là 1/7 tầng testing.
> Phải plan testing strategy từ đầu và allocate thời gian (~30% development time).

---

## Phase 7: Tích hợp & Build

### Mục đích
Kết nối frontend + backend, build production artifacts, và đảm bảo mọi thứ hoạt động end-to-end.

### Checklist

#### 7.1 Frontend-Backend Integration
- [ ] Kết nối API endpoints thực tế (không còn mock data)
- [ ] Test authentication flow end-to-end
- [ ] Test data sync giữa client và server
- [ ] Error handling cho network failures
- [ ] Loading states và error states cho mọi API call
- [ ] Retry logic cho unstable connections
- [ ] Verify request/response types match between FE và BE

#### 7.2 Production Build - Frontend
- [ ] Build production bundle thành công
- [ ] Bundle size acceptable (< target)
- [ ] No TypeScript errors
- [ ] No ESLint errors/warnings
- [ ] Source maps generated (cho error tracking)
- [ ] App icons và splash screen configured
- [ ] Deep linking configured (nếu cần)
- [ ] Push notification integration verified

#### 7.3 Production Build - Backend
- [ ] Build production bundle thành công
- [ ] Database migrations run thành công
- [ ] Seed data prepared (nếu cần)
- [ ] Environment variables documented
- [ ] Health check endpoint working
- [ ] Logging configured cho production
- [ ] CORS configured đúng

#### 7.4 Integration Verification
- [ ] Smoke test: tất cả critical flows hoạt động
- [ ] Data integrity: tạo → sửa → xoá → verify
- [ ] Concurrent users: basic load test
- [ ] Error scenarios: server down, timeout, invalid tokens

### Deliverables
- [ ] Production-ready builds (FE + BE)
- [ ] Integration test results
- [ ] Build artifacts (APK/IPA, Docker image)
- [ ] Deployment guide

### Bài học từ Lunar Reminder App
> **Chưa thực hiện:** Phase này chưa bắt đầu tại thời điểm tạo checklist.
> Frontend code và backend code tồn tại độc lập, chưa integration test.
>
> **Bài học dự kiến:** Integration sớm (ngay khi có 1 endpoint + 1 screen hoạt động)
> giúp phát hiện issues sớm. Đừng đợi "xong hết" rồi mới integrate.

---

## Phase 8: Triển khai

### Mục đích
Deploy ứng dụng lên môi trường staging và production một cách an toàn và có thể rollback.

### Checklist

#### 8.1 Infrastructure Setup
- [ ] Server/hosting provisioned (cloud hoặc local)
- [ ] Database provisioned (production instance)
- [ ] SSL/TLS certificates configured
- [ ] Domain/subdomain configured
- [ ] Firewall rules configured
- [ ] Backup strategy implemented (database + files)
- [ ] Monitoring tools setup (uptime, performance, errors)

#### 8.2 Staging Environment
- [ ] Deploy to staging thành công
- [ ] Staging mirrors production environment
- [ ] QA testing trên staging
- [ ] Stakeholder sign-off trên staging
- [ ] Performance acceptable trên staging

#### 8.3 Production Deployment
- [ ] Deployment runbook documented (step-by-step)
- [ ] Rollback plan documented và tested
- [ ] Database backup trước khi deploy
- [ ] Deploy trong low-traffic window
- [ ] Smoke test ngay sau deploy
- [ ] Monitor logs 30 phút đầu
- [ ] Verify monitoring alerts working

#### 8.4 App Store (Mobile)
- [ ] App Store Connect / Google Play Console accounts setup
- [ ] App listing prepared: screenshots, description, keywords
- [ ] Privacy policy page published
- [ ] Terms of service page published
- [ ] Age rating configured
- [ ] Submit for review (iOS: 1-2 ngày, Android: vài giờ - vài ngày)
- [ ] Plan cho rejection scenarios

#### 8.5 Post-Deployment Verification
- [ ] Tất cả critical flows hoạt động trên production
- [ ] Push notifications working
- [ ] Analytics tracking working
- [ ] Error tracking (Sentry) receiving events
- [ ] Database performing well
- [ ] No 5xx errors trong logs

### Deliverables
- [ ] Production deployment thành công
- [ ] App submitted to stores (hoặc TestFlight/Internal testing)
- [ ] Deployment runbook
- [ ] Monitoring dashboards
- [ ] Rollback plan

### Bài học từ Lunar Reminder App
> **Chưa thực hiện:** Chưa deploy bất kỳ environment nào.
> Plan ban đầu: local server + Cloudflare Tunnel (ADR-007).
>
> **Bài học dự kiến:** Deploy staging sớm (dù chỉ 1 endpoint). Không đợi "hoàn chỉnh".
> CI/CD nên deploy tự động lên staging khi merge to develop.

---

## Phase 9: Launch & Validation

### Mục đích
Ra mắt sản phẩm với người dùng thật, thu thập feedback, và validate giả thuyết ban đầu.

### Checklist

#### 9.1 Pre-Launch
- [ ] Beta testing group tuyển xong (10-50 người)
- [ ] Feedback collection mechanism sẵn sàng (form, in-app, email)
- [ ] Support channel sẵn sàng (email, chat)
- [ ] Known issues list documented (cho support team)
- [ ] Onboarding flow tested và optimized
- [ ] Analytics events tracking verified

#### 9.2 Beta Launch
- [ ] Distribute beta build (TestFlight iOS, Internal Testing Android)
- [ ] Onboard beta testers (hướng dẫn cài đặt, mục đích beta)
- [ ] Thu thập feedback hàng ngày
- [ ] Fix critical bugs ngay (hotfix)
- [ ] Track usage metrics: DAU, sessions, retention
- [ ] Weekly beta report: bugs, feedback, metrics

#### 9.3 Public Launch
- [ ] Fix tất cả critical/high bugs từ beta
- [ ] App store listing finalized
- [ ] Marketing materials ready (nếu có)
- [ ] Launch announcement (social media, email, etc.)
- [ ] Monitor app store reviews
- [ ] Respond to user reviews (đặc biệt negative reviews)

#### 9.4 Post-Launch Validation
- [ ] Key metrics đạt target? (DAU, retention, ratings)
- [ ] User feedback pattern analysis
- [ ] Feature usage analytics: feature nào dùng nhiều, feature nào bỏ qua?
- [ ] Conversion metrics (nếu có monetization)
- [ ] Xác nhận hoặc invalidate original hypotheses

### Deliverables
- [ ] Beta test report
- [ ] Launch metrics dashboard
- [ ] User feedback summary
- [ ] Post-launch retrospective
- [ ] Prioritized backlog cho v1.1

### Bài học từ Lunar Reminder App
> **Chưa thực hiện.** MVP timeline 7 ngày nhưng sau 3 ngày chưa có bản build nào.
>
> **Bài học dự kiến:** Launch sớm, dù chưa hoàn chỉnh. Feedback từ 5 người dùng thật
> giá trị hơn 100 trang specification. "If you're not embarrassed by v1, you launched too late."

---

## Phase 10: Bảo trì & Cải tiến

### Mục đích
Duy trì chất lượng phần mềm và liên tục cải tiến dựa trên feedback và metrics.

### Checklist

#### 10.1 Ongoing Maintenance
- [ ] Monitor errors (Sentry) và fix critical bugs < 24 giờ
- [ ] Dependency updates (monthly): `npm audit` + update minor/patch
- [ ] Database maintenance: backup verification, index optimization
- [ ] SSL certificate renewal (tự động nếu dùng Let's Encrypt)
- [ ] Review và cleanup logs (disk space)
- [ ] Security patches: apply ASAP

#### 10.2 Iteration & Improvement
- [ ] Prioritize backlog dựa trên: user feedback + metrics + business goals
- [ ] Sprint planning cho improvements
- [ ] A/B testing cho UI changes (nếu user base đủ lớn)
- [ ] Performance monitoring và optimization
- [ ] Giảm technical debt (allocate 20% sprint time)

#### 10.3 Retrospectives
- [ ] Sprint retrospective sau mỗi sprint
- [ ] Monthly retrospective: overall project health
- [ ] Quarterly review: roadmap adjustment
- [ ] Document lessons learned

#### 10.4 Growth & Scaling
- [ ] Monitor performance metrics khi user base tăng
- [ ] Database scaling plan (when to migrate, how to shard)
- [ ] CDN cho static assets
- [ ] Caching improvements
- [ ] Migrate từ local hosting lên cloud (nếu cần)

### Deliverables
- [ ] Monthly maintenance report
- [ ] Sprint retrospectives
- [ ] Updated roadmap (quarterly)
- [ ] Performance reports

### Bài học từ Lunar Reminder App
> Phase này chưa áp dụng. Tuy nhiên, bài học từ giai đoạn phát triển:
> **Retrospective nên làm sớm** — không đợi đến khi "xong" mới nhìn lại.
> Dự án này nên có retrospective ngay sau ngày 3 để nhận ra các vấn đề process.

---

## Phụ lục A: AI-Assisted Development Checklist

### Làm việc hiệu quả với Claude / AI Coding Assistants

#### Setup
- [ ] Tạo `CLAUDE.md` (hoặc tương đương) với project rules, conventions, tech stack
- [ ] Cấu hình `.claude/` folder: workflow, roles, gitflow rules
- [ ] Tạo custom skills cho domain-specific tasks
- [ ] Setup `current-state.md` để track progress giữa các sessions

#### Mỗi Session
- [ ] Đọc `current-state.md` trước khi bắt đầu
- [ ] Nêu rõ task, scope, và expected output
- [ ] Sử dụng Plan Mode cho tasks lớn (> 300 LOC)
- [ ] Review output trước khi approve
- [ ] Update `current-state.md` sau mỗi milestone

#### Best Practices
- [ ] **KHÔNG** để AI tự do code mà không review
- [ ] **KHÔNG** generate quá nhiều code cùng lúc (< 300 LOC/step)
- [ ] **PHẢI** commit code trước khi chuyển task
- [ ] **PHẢI** chạy tests sau mỗi code change
- [ ] **PHẢI** verify output (AI có thể "confident but wrong")
- [ ] Sử dụng AI để generate tests, KHÔNG bỏ qua manual review
- [ ] Document AI-generated code quality: đánh dấu sections cần human review

#### Common Pitfalls khi dùng AI
- [ ] AI tạo docs rất nhanh → dẫn đến over-documentation nhưng under-execution
- [ ] AI follow instructions đúng → nhưng instructions có thể sai
- [ ] AI không tự commit, push, hoặc tạo PR → PHẢI yêu cầu explicit
- [ ] AI không biết context giữa sessions → `current-state.md` là critical
- [ ] AI có thể tạo code chạy được nhưng violate best practices → cần review
- [ ] AI context window có giới hạn → break tasks nhỏ

#### Workflow Khi Resume Session
1. Đọc `current-state.md`
2. Kiểm tra git status: có uncommitted changes không?
3. Kiểm tra branch hiện tại: đúng branch cho task?
4. Nêu task tiếp theo, reference documents liên quan
5. Sau khi xong: update `current-state.md`, commit, push

### Bài học từ Lunar Reminder App
> **Làm tốt:** CLAUDE.md rất chi tiết, workflow rules, coding standards, permissions.
> Custom skills tạo cho backend, frontend, lunar calendar. Session tracking qua `current-state.md`.
>
> **Chưa tốt:** AI (Claude) follow rules viết code tốt NHƯNG:
> - Rules nói "commit often" → nhưng chưa commit lần nào vì user chưa yêu cầu explicit
> - Rules nói "tạo branch" → nhưng chưa tạo vì chưa ai trigger workflow
> - Viết 10+ trang docs trước khi viết 1 dòng code thực thi
> - Session tracking chỉ ghi "done" mà chưa ghi "cần commit code"
>
> **Bài học:** AI là tool cực mạnh nhưng cần HUMAN OVERSIGHT ở mỗi bước.
> Checklist này phải được human verify, không phải chỉ AI self-report "done".

---

## Phụ lục B: Template Files cho mọi dự án

### Files cần tạo ngay khi bắt đầu dự án

```
project-root/
│
├── README.md                          # Project overview, setup guide
├── .gitignore                         # Tech-stack specific ignore rules
├── .nvmrc                             # Node.js version
├── .env.example                       # Required environment variables
│
├── docs/
│   ├── PRD.md                         # Product Requirements Document
│   ├── TECHNICAL_SPEC.md              # Architecture & tech decisions
│   ├── DATA_MODEL.md                  # Database schema
│   ├── API_SPEC.md                    # API documentation
│   ├── CHANGE_HISTORY.md              # Decision log
│   └── process/
│       └── PROJECT_LIFECYCLE_CHECKLIST.md  # This file
│
├── .github/
│   ├── PULL_REQUEST_TEMPLATE.md       # PR description template
│   ├── ISSUE_TEMPLATE.md              # Issue template
│   └── workflows/
│       ├── ci.yml                     # CI pipeline
│       └── cd.yml                     # CD pipeline
│
├── frontend/
│   ├── package.json
│   ├── tsconfig.json
│   ├── .eslintrc.js
│   ├── .prettierrc
│   └── src/
│
├── backend/
│   ├── package.json
│   ├── tsconfig.json
│   ├── .eslintrc.js
│   ├── .prettierrc
│   └── src/
│
└── .claude/                           # Nếu dùng AI assistant
    ├── CLAUDE.md                      # AI instructions
    ├── current-state.md               # Session tracking
    └── workflow.md                    # Task workflow
```

### Template Checklist cho mỗi file

| File | Khi nào tạo | Bắt buộc? |
|------|-------------|-----------|
| README.md | Day 1, commit đầu tiên | **Bắt buộc** |
| .gitignore | Day 1, commit đầu tiên | **Bắt buộc** |
| .nvmrc | Day 1 | Recommended |
| .env.example | Khi có environment variable đầu tiên | **Bắt buộc** |
| PRD.md | Trước khi code | **Bắt buộc** |
| TECHNICAL_SPEC.md | Trước khi code | **Bắt buộc** |
| PR template | Khi setup repo | Recommended |
| CI workflow | Khi có code đầu tiên | **Bắt buộc** |
| CLAUDE.md | Nếu dùng AI | Recommended |

---

## Phụ lục C: Common Mistakes - 10 bài học

### Từ Lunar Reminder App và các dự án tương tự

---

### Mistake #1: Documentation trước hết, execution bỏ lại
**Vấn đề:** Dành 2 ngày đầu viết 250+ KB documentation (PRD, Tech Spec, UI/UX, Glossary, Change History) mà chưa commit code lần nào.

**Hậu quả:** Docs chi tiết nhưng chưa validate. Code viết sau có thể không khớp docs. Thời gian viết docs chiếm ~40% timeline mà output thực tế = 0.

**Giải pháp:** Viết docs LEAN: PRD 2-5 trang, Tech Spec 3-5 trang cho MVP. Bổ sung chi tiết SAU khi code. "Working software over comprehensive documentation" (Agile Manifesto).

---

### Mistake #2: Không commit từ đầu
**Vấn đề:** Viết code 3 ngày mà git status vẫn toàn `??` (untracked files). Không commit, không branch, không PR.

**Hậu quả:** Mất toàn bộ history. Không thể rollback. Không thể review changes. Rủi ro mất code (disk crash, rm -rf).

**Giải pháp:** Commit đầu tiên ngay khi tạo project (`git init && git add README.md .gitignore && git commit -m "chore: initial project setup"`). Commit mỗi 30-60 phút làm việc.

---

### Mistake #3: Viết rules mà không enforce
**Vấn đề:** CLAUDE.md có 10+ trang rules chi tiết (git workflow, coding standards, 11-step workflow, mandatory PR reviews) nhưng KHÔNG enforce bất kỳ rule nào.

**Hậu quả:** Rules trở thành decoration. Team (hoặc AI) không follow vì không có accountability.

**Giải pháp:** Enforce bằng tooling: pre-commit hooks (Husky), CI pipeline (lint + test), branch protection (require PR + review). Rules phải có mechanism, không chỉ documentation.

---

### Mistake #4: Pending architecture decisions khi đã code
**Vấn đề:** 2 ADRs quan trọng (Calendar Library AD-011, Lunar Algorithm AD-012) vẫn "Pending research" khi đã bắt đầu implementation.

**Hậu quả:** Có thể phải rewrite code khi quyết định cuối cùng khác assumption.

**Giải pháp:** Resolve TẤT CẢ ADRs TRƯỚC khi code. Nếu chưa quyết được → prototype/spike nhỏ → decide → rồi mới implement chính thức.

---

### Mistake #5: Over-planning, under-executing
**Vấn đề:** Tạo folder structure phức tạp (planning/, tasks/backlog/, tasks/sprint-01/, platform-specific/, automation/, skills/) từ ngày đầu nhưng hầu hết folder trống hoặc chỉ có placeholder.

**Hậu quả:** Complexity không cần thiết. Developer mới mở project thấy 50 folders/files nhưng không rõ cái nào quan trọng.

**Giải pháp:** Tạo folder/file CHỈ KHI cần dùng. Start flat, refactor thành hierarchy khi cần. YAGNI (You Ain't Gonna Need It).

---

### Mistake #6: Không test với người dùng thật sớm
**Vấn đề:** 3 ngày phát triển, chưa có build nào để test. Chưa cho ai (kể cả bạn bè, gia đình) dùng thử.

**Hậu quả:** Assumptions chưa validate. Có thể xây sai feature.

**Giải pháp:** Deploy lên TestFlight/Internal Testing ngay khi có flow đầu tiên hoạt động (dù chỉ 1 screen). Thu thập feedback sớm.

---

### Mistake #7: Chỉ test utilities, bỏ qua integration
**Vấn đề:** 160 unit tests cho dateUtils, validationUtils, colorUtils (coverage 97%) nhưng 0 tests cho hooks, components, screens, API endpoints.

**Hậu quả:** Confidence cao nhưng sai chỗ. Utils ít khi có bug nghiêm trọng. Bugs thường ở integration points (API calls, state management, navigation).

**Giải pháp:** Testing pyramid: Unit tests (nhiều, nhanh) → Integration tests (vừa, trung bình) → E2E tests (ít, chậm). Phải có ít nhất integration tests cho critical paths.

---

### Mistake #8: CI/CD "sẽ làm sau"
**Vấn đề:** GitHub Actions template files đã tạo nhưng chưa cấu hình thực tế. CI chưa chạy lần nào.

**Hậu quả:** Code quality không được enforce tự động. Bugs lọt qua. Deploy thủ công, error-prone.

**Giải pháp:** Setup CI pipeline ngay ngày đầu (dù chỉ chạy `npm run lint && npm test`). Bổ sung steps sau. CI pipeline đơn giản chạy > CI pipeline hoàn hảo nhưng chưa tồn tại.

---

### Mistake #9: Scope MVP quá lớn
**Vấn đề:** MVP bao gồm: auth (JWT + social login), CRUD events, lunar calendar, notifications, sharing, groups, widgets, offline-first sync, calendar export, multi-language.

**Hậu quả:** Không thể ship trong 7 ngày. Dàn trải effort. Chưa feature nào hoàn chỉnh end-to-end.

**Giải pháp:** MVP = 1 core flow hoạt động end-to-end. Cho Lunar Reminder: tạo event âm lịch → nhận notification. Không cần auth, sharing, groups, widgets cho v0.1.

---

### Mistake #10: AI làm nhiều, human oversight ít
**Vấn đề:** AI (Claude) generate code, docs, tests rất nhanh. Human approve nhưng không verify đủ sâu. Kết quả: nhiều output nhưng ít outcome.

**Hậu quả:** Tạo cảm giác progress nhưng thực tế ít tiến triển. Code chưa commit, chưa deploy, chưa test thực tế.

**Giải pháp:** Sau mỗi AI session, human verify checklist:
1. Code đã commit?
2. Tests đã chạy pass?
3. PR đã tạo?
4. Feature demo được cho stakeholder?

Nếu 4 câu trả lời đều "chưa" → session đó chưa tạo giá trị thực.

---

## Phụ lục D: Sequence Diagram - Thứ tự lý tưởng

### Thứ tự lý tưởng cho dự án mobile app

```
Week 0 (Preparation)
│
├─ Day 1: Ý tưởng → Problem Statement → Personas
├─ Day 2: Competitor research → Feasibility → Go/No-Go
│
Week 1 (Design & Setup)
│
├─ Day 3: PRD (lean, 3-5 trang) → Scope MVP → Task breakdown
├─ Day 4: Tech Spec (lean) → ADRs (ALL resolved) → Data Model
├─ Day 5: ★ FIRST COMMIT ★
│         git init → .gitignore → README → scaffold projects
│         Setup CI pipeline (lint + test)
│         Setup branch protection
│         Pre-commit hooks
│
Week 2-3 (Core Development)
│
├─ Day 6-7:   Feature 1 (core flow) → Branch → Code → Test → PR → Merge
├─ Day 8-9:   Feature 2 → Branch → Code → Test → PR → Merge
├─ Day 10:    Integration test FE + BE
│             Deploy staging
│             ★ FIRST DEMO ★ cho stakeholders
├─ Day 11-12: Feature 3-4 → Iterative development
├─ Day 13:    Bug fixes từ demo feedback
├─ Day 14:    Sprint retrospective
│
Week 4 (Polish & Launch)
│
├─ Day 15-16: Bug fixes, performance optimization
├─ Day 17:    Security review, accessibility check
├─ Day 18:    Production deploy, beta distribution
├─ Day 19:    ★ BETA LAUNCH ★ (10-50 users)
├─ Day 20:    Monitor, collect feedback
├─ Day 21:    Sprint retrospective, plan next iteration
│
Week 5+ (Iteration)
│
├─ Fix bugs từ beta feedback
├─ Add features based on user demand
├─ ★ PUBLIC LAUNCH ★ when ready
└─ Continuous improvement
```

### Key Milestones (không bỏ qua)

```
Milestone 1: ★ FIRST COMMIT ★
  Khi nào: Ngay khi có .gitignore + README
  Rule: KHÔNG viết business logic trước first commit

Milestone 2: ★ CI PIPELINE GREEN ★
  Khi nào: Ngay sau first commit
  Rule: Mọi code sau đó PHẢI qua CI

Milestone 3: ★ FIRST PR MERGED ★
  Khi nào: Feature đầu tiên hoàn thành
  Rule: Có review (self-review nếu solo)

Milestone 4: ★ FIRST DEMO ★
  Khi nào: 1 core flow hoạt động end-to-end
  Rule: Demo cho ít nhất 1 stakeholder

Milestone 5: ★ FIRST DEPLOY ★
  Khi nào: Staging environment
  Rule: Không đợi "hoàn chỉnh" mới deploy

Milestone 6: ★ FIRST USER ★
  Khi nào: Beta build cho người dùng thật
  Rule: Feedback > Perfection
```

### Nguyên tắc thứ tự (KHÔNG ĐẢO)

```
1. Idea + Research        ─── không code
2. Plan + Design          ─── không code
3. Git init + FIRST COMMIT ← ★ điểm khởi đầu code ★
4. CI/CD setup            ─── enforce quality
5. Scaffold + Build check ─── verify setup
6. Feature development    ─── branch per feature
7. Integration testing    ─── FE + BE together
8. Deploy staging         ─── real environment
9. Beta testing           ─── real users
10. Production launch     ─── go live
```

**Quy tắc vàng:** Mỗi bước phải HOÀN THÀNH và VERIFY trước khi sang bước tiếp theo.
Không skip bước. Không "sẽ làm sau". Nếu bước 3 chưa xong (chưa commit) → KHÔNG tiến sang bước 6 (viết features).

---

## Tổng kết

### Checklist nhanh - "Dự án tôi có on track không?"

| Câu hỏi | Trả lời | Nếu "Không" |
|----------|---------|--------------|
| Đã commit code chưa? | Có/Không | DỪNG. Commit ngay. |
| CI pipeline chạy không? | Có/Không | Setup CI trước khi code tiếp. |
| Có branch strategy không? | Có/Không | Tạo branch cho feature hiện tại. |
| PR/MR đã tạo chưa? | Có/Không | Tạo PR, dù self-review. |
| Tests chạy pass không? | Có/Không | Fix tests trước khi code mới. |
| Demo được cho ai chưa? | Có/Không | Deploy staging, schedule demo. |
| Người dùng thật đã thử chưa? | Có/Không | Gửi beta build cho 5 người. |
| Pending decisions còn không? | Có/Không | Resolve trước khi code tiếp. |

### Một câu tổng kết

> **Ship early. Ship often. Fix later. Perfection is the enemy of progress.**
> Nhưng ship phải có: commit, CI, review, tests. Không ship code chưa commit.

---

**Document Version History:**

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-02-04 | Initial creation, 10 phases + 4 appendices |

---

*Tài liệu này có thể copy và dùng cho bất kỳ dự án phần mềm nào. Thay đổi "Lunar Reminder App" thành tên dự án mới và cập nhật bài học riêng.*
