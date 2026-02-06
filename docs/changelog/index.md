# Change History & Documentation Log
# Lunar Reminder App

---

**Purpose:** Track all documents, decisions, changes, and modifications for future reference and maintenance.

**Last Updated:** 2026-02-02
**Maintained By:** Product Manager / Tech Lead

---

## Table of Contents

1. [Document Creation Log](#1-document-creation-log)
2. [Decision Log](#2-decision-log)
3. [Change History](#3-change-history)
4. [Future Maintenance Guide](#4-future-maintenance-guide)

---

## 1. Document Creation Log

### Session 1: Initial Planning & Core Documents
**Date:** 2026-02-02
**Duration:** Request 1-2
**Status:** Complete

#### Documents Created:

| Document | Created | Size | Status |
|----------|---------|------|--------|
| README.md | 2026-02-02 | ~9.3 KB | Complete |
| docs/requirements/ (was 01_PRODUCT_REQUIREMENTS.md) | 2026-02-02 | ~82 KB | Approved |
| docs/architecture/ (was 02_TECHNICAL_SPEC.md) | 2026-02-02 | ~105 KB | Complete |
| docs/ui-ux/ (was 03_UI_UX_SPEC.md) | 2026-02-02 | ~45 KB | Complete |
| docs/reference/glossary.md (was 07_GLOSSARY.md) | 2026-02-02 | ~12 KB | Complete |
| .gitignore | 2026-02-02 | ~2.5 KB | Complete |

### Folder Structure Created:

```
lunar-reminder-app/
├── README.md
├── .gitignore
├── docs/
│   ├── 01_PRODUCT_REQUIREMENTS.md
│   ├── 02_TECHNICAL_SPEC.md
│   ├── 03_UI_UX_SPEC.md
│   ├── 07_GLOSSARY.md
│   └── changelog/
│       ├── index.md (this file)
│       └── decisions/
│           └── ADR-*.md
├── planning/
├── tasks/
│   ├── backlog/
│   └── sprint-01/
├── platform-specific/
├── automation/
├── frontend/
└── backend/
```

---

## 2. Decision Log

### Architecture Decisions (ADRs)

All architecture decisions are documented as Architecture Decision Records (ADRs) in the `decisions/` folder.

| ADR | Title | Status | Date |
|-----|-------|--------|------|
| [ADR-001](decisions/ADR-001-mobile-platform.md) | Mobile Platform Choice (React Native) | Approved | 2026-02-02 |
| [ADR-002](decisions/ADR-002-backend-framework.md) | Backend Framework (NestJS) | Approved | 2026-02-02 |
| [ADR-003](decisions/ADR-003-database-choice.md) | Database Choice (PostgreSQL) | Approved | 2026-02-02 |
| [ADR-004](decisions/ADR-004-state-management.md) | State Management (Redux Toolkit) | Approved | 2026-02-02 |
| [ADR-005](decisions/ADR-005-local-database.md) | Local Database Mobile (SQLite) | Approved | 2026-02-02 |
| [ADR-006](decisions/ADR-006-push-notifications.md) | Push Notifications (OneSignal) | Approved | 2026-02-02 |
| [ADR-007](decisions/ADR-007-hosting-strategy.md) | Hosting Strategy (Local + Cloudflare) | Approved | 2026-02-02 |
| [ADR-008](decisions/ADR-008-authentication.md) | Authentication (Custom JWT) | Approved | 2026-02-02 |
| [ADR-009](decisions/ADR-009-offline-first.md) | Offline-First Approach | Approved | 2026-02-02 |
| [ADR-010](decisions/ADR-010-ui-components.md) | UI Component Strategy | Approved | 2026-02-02 |
| [ADR-011](decisions/ADR-011-calendar-library.md) | Calendar Library | Pending | 2026-02-02 |
| [ADR-012](decisions/ADR-012-lunar-algorithm.md) | Lunar Calendar Algorithm | Pending | 2026-02-02 |

### Feature Decisions

| ID | Decision | Status |
|----|----------|--------|
| FD-001 | Login is optional, offline-first | Approved |
| FD-002 | 6 event types: Gio/Ky, Sinh nhat, Le hoi, Ngay cuoi, Ky niem, Khac | Approved |
| FD-003 | Gio: 3 days before (2-4-4-4 times/day); Other: 1 day before (2-4 times/day) | Approved |
| FD-004 | Leap month: User chooses 1st, 2nd, or both occurrences | Approved |
| FD-005 | Share via link, requires app install | Approved |
| FD-006 | One-way sync, export yearly to calendar | Approved |
| FD-007 | Widget support in MVP (3 sizes) | Approved |
| FD-008 | Vietnamese + English in MVP | Approved |

### Design Decisions

| ID | Decision | Status |
|----|----------|--------|
| DD-001 | Red (#D32F2F) + Gold (#FBC02D) as primary colors | Approved |
| DD-002 | Unique color per event type | Approved |
| DD-003 | System fonts (SF Pro, Roboto) | Approved |
| DD-004 | Vietnamese patterns: clouds, dragons, lotus, bronze drum | Approved |
| DD-005 | Bottom tab navigation (4 tabs) | Approved |

### Timeline Decisions

| ID | Decision | Status |
|----|----------|--------|
| TD-001 | 7 days MVP timeline | Approved |
| TD-002 | User reviews MR, Claude codes and iterates | Approved |
| TD-003 | Request frequency: every 5 hours or token reset | Active |

---

## 3. Change History

### Version 1.0 - Initial Release (2026-02-02)

**Documentation Phase**

**Changes:**
- Created project structure
- Created README.md with project overview
- Created Product Requirements Document (82KB)
- Created Technical Specification (105KB)
- Created UI/UX Specification (45KB)
- Created Glossary (12KB)
- Created .gitignore
- Created folder structure

**Decisions Made:**
- All architecture decisions (AD-001 to AD-012)
- All feature decisions (FD-001 to FD-008)
- All design decisions (DD-001 to DD-005)
- All timeline decisions (TD-001 to TD-003)

**Status:** Phase 1 Complete

---

### Version 1.1 - Planning Phase (Upcoming)

**Planned Changes:**
- Create ROLES_AND_RESPONSIBILITIES.md
- Create PROJECT_ROADMAP.md
- Create TASK_BREAKDOWN.md
- Create Jira tickets export
- Create Sprint planning docs

**Status:** Planned for Request 3-4

---

### Version 1.2 - Platform Setup (Upcoming)

**Planned Changes:**
- React Native setup guide
- NestJS setup guide
- Database setup guide
- Cloudflare Tunnel guide
- CI/CD pipeline configs

**Status:** Planned for Request 5-8

---

## 4. Future Maintenance Guide

### 4.1 When to Update Documents

| Document | Update When | Update By |
|----------|-------------|-----------|
| Product Requirements | New features, scope changes | Product Manager |
| Technical Spec | Architecture changes, API updates | Tech Lead |
| UI/UX Spec | Design changes, new screens | UI/UX Designer |
| Glossary | New terms introduced | Anyone |
| Change History | Any document updated | PM / Tech Lead |

### 4.2 Version Numbering

**Format:** MAJOR.MINOR.PATCH

- **MAJOR (1.x.x):** Breaking changes, major overhaul
- **MINOR (x.1.x):** New features, non-breaking changes
- **PATCH (x.x.1):** Bug fixes, typo corrections

**Current Version:** 1.0

### 4.3 Document Review Schedule

**Quarterly Reviews:** March, June, September, December 2026

**Review Checklist:**
- All documents up to date
- Decisions still valid
- Tech stack still appropriate
- No outdated information
- All links working

### 4.4 Git Strategy

- Commit docs to Git repository
- Tag releases: `v1.0`, `v1.1`, etc.
- Branch for major doc updates
- Pull requests for review

---

## 5. Quick Reference

### Key Decisions Summary

**Platform:** React Native + NestJS + PostgreSQL
**Hosting:** Local + Cloudflare Tunnel (Cloud later)
**Notifications:** OneSignal
**Auth:** JWT + Social login
**Strategy:** Offline-first
**Timeline:** 7 days MVP

### Important Links

**Documentation:**
- Requirements: `docs/requirements/`
- Architecture: `docs/architecture/`
- UI/UX: `docs/ui-ux/index.md`
- Glossary: `docs/reference/glossary.md`
- Implementation Specs: `openspec/specs/capabilities/`

**External References:**
- React Native: https://reactnative.dev
- NestJS: https://nestjs.com
- PostgreSQL: https://www.postgresql.org
- OneSignal: https://onesignal.com
- Cloudflare Tunnel: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/

---

## 6. Next Steps Tracker

### Immediate (Request 2-3):
- [ ] Create planning documents (roadmap, tasks)
- [ ] Create Jira tickets
- [ ] Create platform setup guides
- [ ] Create API specification document
- [ ] Create user flow diagrams

### Short-term (Request 4-8):
- [ ] Setup development environment
- [ ] Initialize Git repository
- [ ] Create CI/CD pipelines
- [ ] Setup Cloudflare Tunnel
- [ ] Begin implementation

### Medium-term (Week 2):
- [ ] Complete MVP development
- [ ] Testing & QA
- [ ] Launch preparation
- [ ] User documentation

---

**End of Change History**

**Last Updated:** 2026-02-02
**Next Review:** 2026-02-09 (after Week 1)
