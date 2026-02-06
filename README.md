# 🌙 Lunar Reminder App - Ứng dụng Nhắc nhở Lịch Âm Việt Nam

> Ứng dụng mobile giúp người Việt Nam nhớ và quản lý các sự kiện quan trọng theo lịch âm

## 📋 Project Overview

**Version:** 1.0.0 (MVP)  
**Timeline:** 1 tuần (7 ngày)  
**Status:** 🟡 In Planning  
**Last Updated:** 2026-02-02

### 🎯 Vision

Giúp mọi người Việt Nam, đặc biệt là thế hệ trẻ, không bỏ lỡ các ngày quan trọng theo lịch âm như ngày giỗ, chạp, sinh nhật, và các sự kiện văn hóa truyền thống.

### ⭐ Key Features

- ✅ Quản lý sự kiện theo lịch âm và dương
- ✅ Chuyển đổi tự động lịch âm ↔ dương Việt Nam
- ✅ Thông báo thông minh với tần suất tăng dần
- ✅ Phân loại sự kiện (Giỗ, Sinh nhật, Lễ, Ngày cưới...)
- ✅ Chia sẻ sự kiện với gia đình
- ✅ Export sang Google Calendar
- ✅ Widget hiển thị sự kiện sắp tới
- ✅ Giao diện truyền thống Việt Nam
- ✅ Hỗ trợ đa ngôn ngữ (Tiếng Việt, English)

## 🛠️ Tech Stack

### Frontend (Mobile App)
- **Framework:** React Native
- **State Management:** Redux Toolkit
- **Navigation:** React Navigation
- **UI:** Custom Components + Calendar Library
- **Language:** TypeScript
- **Testing:** Jest + React Native Testing Library

### Backend (API Server)
- **Framework:** NestJS
- **Database:** PostgreSQL
- **ORM:** TypeORM
- **Authentication:** Custom JWT
- **Push Notifications:** OneSignal
- **Language:** TypeScript

### Infrastructure
- **Development:** Local Machine + Cloudflare Tunnel
- **Database:** PostgreSQL (Local)
- **Caching:** Redis (Local)
- **Hosting:** Local → Cloud (khi scale)

### Development Tools
- **Version Control:** Git + GitHub
- **Project Management:** Jira
- **CI/CD:** GitHub Actions
- **Code Quality:** ESLint + Prettier
- **Documentation:** Markdown + OpenAPI

## 📁 Project Structure

```
lunar-reminder-app/
├── README.md                          # This file
├── .gitignore
├── LICENSE
│
├── docs/                              # 📚 All documentation
│   ├── index.md                      # Documentation hub
│   ├── requirements/                 # Feature requirements
│   ├── architecture/                 # System design & tech stack
│   ├── api/                          # REST API endpoints
│   ├── data-model/                   # Database schema
│   ├── ui-ux/                        # Design system & screens
│   ├── reference/glossary.md         # Terms & definitions
│   ├── process/                      # Workflow & lifecycle docs
│   └── _archive/                     # Legacy docs (read-only)
│
├── planning/                          # 📅 Project planning
│   ├── ROLES_AND_RESPONSIBILITIES.md
│   ├── PROJECT_ROADMAP.md            # 7-day sprint plan
│   ├── TASK_BREAKDOWN.md             # Epic → Story → Task
│   └── JIRA_EXPORT.csv               # Jira tickets ready to import
│
├── tasks/                             # 🎫 Task tracking
│   ├── backlog/
│   │   ├── epic-01-authentication.md
│   │   ├── epic-02-event-management.md
│   │   ├── epic-03-notification-system.md
│   │   ├── epic-04-lunar-calendar.md
│   │   ├── epic-05-sharing.md
│   │   └── epic-06-ui-polish.md
│   └── sprint-01/
│       └── day-01/
│
├── frontend/                          # 📱 React Native app
│   ├── README.md
│   ├── package.json
│   ├── tsconfig.json
│   ├── src/
│   └── __tests__/
│
├── backend/                           # 🔧 NestJS API
│   ├── README.md
│   ├── package.json
│   ├── tsconfig.json
│   ├── src/
│   └── test/
│
├── platform-specific/                 # 🔧 Platform guides
│   ├── react-native/
│   │   ├── SETUP.md
│   │   ├── DEPENDENCIES.md
│   │   ├── FOLDER_STRUCTURE.md
│   │   ├── CODING_STANDARDS.md
│   │   └── COMPONENT_LIBRARY.md
│   ├── nestjs/
│   │   ├── SETUP.md
│   │   ├── PROJECT_STRUCTURE.md
│   │   ├── CODING_STANDARDS.md
│   │   └── CLOUDFLARE_TUNNEL.md
│   └── database/
│       ├── POSTGRES_SETUP.md
│       └── MIGRATIONS.md
│
├── automation/                        # 🤖 CI/CD & Scripts
│   ├── github-workflows/
│   │   ├── frontend-ci.yml
│   │   ├── backend-ci.yml
│   │   └── deploy.yml
│   ├── scripts/
│   │   ├── setup-local.sh
│   │   ├── db-seed.sh
│   │   └── generate-pr.sh
│   └── templates/
│       ├── PR_TEMPLATE.md
│       ├── ISSUE_TEMPLATE.md
│       └── MR_CHECKLIST.md
│
└── skills/                            # 🧠 Custom Claude Skills
    ├── lunar-calendar-skill.md
    ├── vietnam-culture-skill.md
    └── notification-patterns-skill.md
```

## 🚀 Quick Start

### Prerequisites
- Node.js 18+ & npm
- PostgreSQL 14+
- Redis (optional for caching)
- Git
- React Native development environment

### Setup Local Development

```bash
# Clone repository
git clone https://github.com/your-org/lunar-reminder-app.git
cd lunar-reminder-app

# Setup backend
cd backend
npm install
npm run db:migrate
npm run db:seed
npm run start:dev

# Setup frontend (new terminal)
cd frontend
npm install
npm run ios    # For iOS
npm run android # For Android
```

### Setup Cloudflare Tunnel

```bash
# Install cloudflared
brew install cloudflare/cloudflare/cloudflared

# Authenticate
cloudflared tunnel login

# Create tunnel
cloudflared tunnel create lunar-reminder-api

# Configure tunnel (see platform-specific/nestjs/CLOUDFLARE_TUNNEL.md)
```

## 👥 Team & Roles

- **Product Manager:** Review, approve MRs, manage backlog
- **Solution Architect:** Design system architecture
- **Frontend Developer:** React Native development
- **Backend Developer:** NestJS API development
- **QA Engineer:** Testing & quality assurance
- **DevOps:** CI/CD & infrastructure

## 📖 Documentation

### Must-Read Documents (In Order)
1. [Documentation Hub](./docs/index.md) - Start here
2. [Product Overview](./docs/requirements/overview.md) - Product vision
3. [Architecture](./docs/architecture/index.md) - System design
4. [Implementation Workflow](./docs/process/IMPLEMENTATION_WORKFLOW.md) - How we work

### Development Guides
- [Backend Setup](./docs/guides/BACKEND_SETUP.md)
- [Frontend Setup](./docs/guides/FRONTEND_SETUP.md)
- [MR Checklist](./docs/guides/MR_CHECKLIST.md)

### Reference
- [API Documentation](./docs/api/index.md) - REST API endpoints
- [Data Model](./docs/data-model/index.md) - Database schema
- [Glossary](./docs/reference/glossary.md) - Terms & definitions
- [OpenSpec Capabilities](./openspec/specs/capabilities/) - Implementation specs

## 🎯 Development Workflow

### 1. Pick a Task from Jira
- Sprint board: https://your-jira.atlassian.net
- Assign task to yourself
- Move to "In Progress"

### 2. Create Feature Branch
```bash
git checkout develop
git pull origin develop
git checkout -b feature/TASK-XXX-description
```

### 3. Development
- Write code following coding standards
- Write tests (coverage > 80%)
- Run linter & formatter

### 4. Create Pull Request
```bash
git add .
git commit -m "feat(module): TASK-XXX description"
git push origin feature/TASK-XXX-description
```
- Create PR on GitHub
- Fill PR template
- Request review from PM

### 5. Review & Merge
- PM reviews code
- PM adds comments if needed
- Developer fixes comments
- PM approves & merges to `develop`

### 6. CI/CD Auto Deployment
- GitHub Actions runs tests
- Auto deploy to staging (local server via Cloudflare Tunnel)

## 📊 Progress Tracking

### Week 1 - MVP Development (Current)
- **Day 1-2:** ✅ Planning & Documentation
- **Day 3-4:** 🔄 Core Features Development
- **Day 5-6:** ⏳ Testing & Polish
- **Day 7:** ⏳ Launch Preparation

### Metrics
- **Target:** MVP with core features
- **Screens:** 8-10 main screens
- **API Endpoints:** 15-20 endpoints
- **Test Coverage:** > 80%
- **Performance:** < 2s load time

## 🐛 Issue Tracking

- **Jira Board:** https://your-jira.atlassian.net
- **GitHub Issues:** For technical bugs only
- **Bug Priority:** Critical → High → Medium → Low

## 📝 Contributing

1. Read [Coding Standards](./platform-specific/react-native/CODING_STANDARDS.md)
2. Create feature branch from `develop`
3. Follow commit message convention
4. Write tests for new features
5. Update documentation
6. Create PR using template

## 📜 License

TBD - To be decided

## 📞 Contact & Support

- **PM:** [Your contact]
- **Tech Lead:** [Your contact]
- **Slack:** #lunar-reminder-dev

## 🔗 Links

- **Jira:** https://your-jira.atlassian.net
- **GitHub:** https://github.com/your-org/lunar-reminder-app
- **Figma:** [Design files link]
- **Staging API:** https://your-tunnel.trycloudflare.com
- **Documentation:** [Wiki link]

---

**Built with ❤️ for Vietnamese culture and traditions**

Last updated: 2026-02-02
