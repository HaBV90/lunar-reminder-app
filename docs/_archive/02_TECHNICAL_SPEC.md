# Technical Specification
# Lunar Reminder App

---

**Document Version:** 1.0  
**Last Updated:** 2026-02-02  
**Status:** ✅ Approved  
**Owner:** Solution Architect / Tech Lead

---

## Table of Contents

1. [System Overview](#1-system-overview)
2. [Architecture Design](#2-architecture-design)
3. [Technology Stack](#3-technology-stack)
4. [Database Design](#4-database-design)
5. [API Design](#5-api-design)
6. [Frontend Architecture](#6-frontend-architecture)
7. [Backend Architecture](#7-backend-architecture)
8. [Lunar Calendar Implementation](#8-lunar-calendar-implementation)
9. [Notification System](#9-notification-system)
10. [Data Synchronization](#10-data-synchronization)
11. [Security](#11-security)
12. [Performance Optimization](#12-performance-optimization)
13. [Deployment Architecture](#13-deployment-architecture)
14. [Development Environment](#14-development-environment)

---

## 1. System Overview

### 1.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     MOBILE CLIENTS                           │
│  ┌──────────────────────┐    ┌──────────────────────┐      │
│  │    iOS App           │    │   Android App         │      │
│  │  (React Native)      │    │  (React Native)       │      │
│  └──────────────────────┘    └──────────────────────┘      │
│             │                            │                   │
│             └────────────────────────────┘                   │
│                          │                                   │
└──────────────────────────┼───────────────────────────────────┘
                           │
                           │ HTTPS/REST API
                           │
┌──────────────────────────▼───────────────────────────────────┐
│              CLOUDFLARE TUNNEL (Expose)                      │
└──────────────────────────┬───────────────────────────────────┘
                           │
┌──────────────────────────▼───────────────────────────────────┐
│                  LOCAL BACKEND SERVER                         │
│  ┌────────────────────────────────────────────────┐          │
│  │          NestJS API Server                     │          │
│  │  ┌──────────────┐  ┌──────────────┐           │          │
│  │  │ Auth Module  │  │ Event Module │           │          │
│  │  └──────────────┘  └──────────────┘           │          │
│  │  ┌──────────────┐  ┌──────────────┐           │          │
│  │  │ Lunar Module │  │ Notif Module │           │          │
│  │  └──────────────┘  └──────────────┘           │          │
│  └────────────────────────────────────────────────┘          │
│                          │                                   │
│  ┌────────────────────────────────────────────────┐          │
│  │         PostgreSQL Database                     │          │
│  │  - Users, Events, Groups, Shares               │          │
│  └────────────────────────────────────────────────┘          │
│                          │                                   │
│  ┌────────────────────────────────────────────────┐          │
│  │         Redis Cache (Optional)                  │          │
│  │  - Session cache, API cache                    │          │
│  └────────────────────────────────────────────────┘          │
└───────────────────────────────────────────────────────────────┘
                           │
┌──────────────────────────▼───────────────────────────────────┐
│              EXTERNAL SERVICES                                │
│  ┌──────────────────┐    ┌──────────────────┐               │
│  │   OneSignal      │    │  Google Calendar │               │
│  │  (Push Notif)    │    │     API          │               │
│  └──────────────────┘    └──────────────────┘               │
└───────────────────────────────────────────────────────────────┘
```

### 1.2 System Components

**Mobile App (React Native):**
- Cross-platform iOS/Android
- Offline-first architecture
- Local storage (SQLite/Realm)
- Redux Toolkit state management
- React Navigation routing

**Backend API (NestJS):**
- RESTful API
- JWT authentication
- TypeORM for database
- Modular architecture
- Scheduled jobs for notifications

**Database (PostgreSQL):**
- Primary data store
- ACID transactions
- Full-text search
- JSON support for metadata

**Cache Layer (Redis):**
- Session storage
- API response caching
- Rate limiting counters

**External Services:**
- OneSignal: Push notifications
- Google Calendar API: Export functionality
- Cloudflare Tunnel: Expose local server

---

## 2. Architecture Design

### 2.1 Architecture Principles

1. **Offline-First:**
   - App fully functional offline
   - Local database (SQLite/Realm)
   - Background sync when online

2. **Microservices-Ready:**
   - Modular NestJS architecture
   - Easy to split into microservices later
   - Independent module deployment

3. **Scalability:**
   - Horizontal scaling ready
   - Database connection pooling
   - Caching strategy

4. **Security:**
   - JWT-based authentication
   - HTTPS only
   - Input validation
   - SQL injection prevention

5. **Maintainability:**
   - TypeScript throughout
   - Clean architecture
   - Comprehensive testing
   - Clear documentation

### 2.2 Design Patterns

**Frontend:**
- Redux Pattern for state management
- Container/Presenter pattern for components
- HOC (Higher-Order Components) for reusable logic
- Custom Hooks for business logic

**Backend:**
- Repository Pattern (TypeORM)
- Service Layer Pattern
- Dependency Injection (NestJS)
- Strategy Pattern (notification scheduling)

### 2.3 Data Flow

**Event Creation Flow:**
```
User Input → Redux Action → Local Save (SQLite) 
  → API Call → Backend Validation → PostgreSQL Save 
  → Schedule Notifications → Response → Update Redux → UI Update
```

**Notification Flow:**
```
Scheduled Job (Cron) → Check Upcoming Events → Calculate Recipients
  → Create OneSignal Notifications → Send → Log Delivery
```

**Sync Flow:**
```
App Launch → Check Network → API Call (GET /sync?lastSyncTime=X)
  → Server: Find Changes → Response with Delta → Merge Local + Remote
  → Conflict Resolution (Last Write Wins) → Update Local DB
```

---

## 3. Technology Stack

### 3.1 Frontend (Mobile App)

**Core:**
- **React Native:** 0.73+ (latest stable)
- **TypeScript:** 5.0+
- **React:** 18+

**State Management:**
- **Redux Toolkit:** 2.0+
- **Redux Persist:** For offline state
- **RTK Query:** For API calls (optional)

**Navigation:**
- **React Navigation:** v6+
- Stack, Tab, Drawer navigators

**UI Components:**
- **Custom components** (built from scratch)
- **React Native Vector Icons:** For icons
- **Calendar Library:** TBD (research needed)
  - Options: `react-native-calendars`, `react-native-calendar-picker`

**Local Database:**
- **SQLite** via `react-native-sqlite-storage`
- Or **Realm** (alternative, better performance)
- **Recommendation:** SQLite for simplicity

**Utilities:**
- **Axios:** HTTP client
- **Day.js:** Date manipulation
- **React Hook Form:** Form handling
- **Yup:** Validation schemas

**Development:**
- **ESLint:** Linting
- **Prettier:** Code formatting
- **Jest:** Unit testing
- **React Native Testing Library:** Component testing
- **Detox:** E2E testing (optional)

### 3.2 Backend (API Server)

**Core:**
- **NestJS:** 10+
- **TypeScript:** 5.0+
- **Node.js:** 18+ LTS

**Database:**
- **PostgreSQL:** 14+
- **TypeORM:** 0.3+ (ORM)
- **pg:** PostgreSQL driver

**Authentication:**
- **JWT:** `@nestjs/jwt`
- **Passport:** `@nestjs/passport`
- **bcrypt:** Password hashing

**Caching:**
- **Redis:** 7+
- **@nestjs/cache-manager**

**Task Scheduling:**
- **@nestjs/schedule:** Cron jobs
- **node-cron:** Advanced scheduling

**Validation:**
- **class-validator:** DTO validation
- **class-transformer:** Object transformation

**API Documentation:**
- **@nestjs/swagger:** OpenAPI spec generation

**Testing:**
- **Jest:** Unit testing
- **Supertest:** API testing

**Utilities:**
- **Moment.js** or **Day.js:** Date handling
- **Lunar Calendar Library:** TBD (research needed)
  - Options: Custom implementation, `lunar-calendar`, Vietnamese lunar libs

### 3.3 Infrastructure

**Version Control:**
- **Git + GitHub**

**CI/CD:**
- **GitHub Actions**

**Monitoring (Future):**
- **Sentry:** Error tracking
- **Datadog / Grafana:** Metrics

**Hosting (Current):**
- **Local Machine:** Development + staging + "production"
- **Cloudflare Tunnel:** Expose to internet

**Hosting (Future - when scale):**
- **Railway.app:** Quick deploy (recommended)
- **AWS:** Production-grade (EC2, RDS, S3)
- **Google Cloud:** Alternative

### 3.4 External Services

**OneSignal:**
- Free tier: 10K subscribers
- Push notifications to iOS & Android
- REST API for sending
- Dashboard for analytics

**Google Calendar API:**
- OAuth 2.0 for user consent
- Create events programmatically
- Set reminders

**Cloudflare Tunnel:**
- Free
- No need for public IP
- Stable connection
- CLI tool: `cloudflared`

---

## 4. Database Design

### 4.1 PostgreSQL Schema

#### 4.1.1 Entity Relationship Diagram

```
┌─────────────────┐         ┌─────────────────┐
│     users       │1       *│     events      │
│─────────────────│◄────────│─────────────────│
│ id (PK)         │         │ id (PK)         │
│ email           │         │ user_id (FK)    │
│ phone           │         │ name            │
│ password_hash   │         │ type            │
│ created_at      │         │ calendar_type   │
│ updated_at      │         │ lunar_date      │
└─────────────────┘         │ solar_date      │
                            │ notes           │
                            │ notify_enabled  │
                            │ notify_config   │
                            │ created_at      │
                            │ updated_at      │
                            └─────────────────┘
                                     │
                                     │
                                     │*
                            ┌────────▼────────┐
                            │ event_groups    │
                            │─────────────────│
                            │ id (PK)         │
                            │ event_id (FK)   │
                            │ group_id (FK)   │
                            └─────────────────┘
                                     │*
┌─────────────────┐                 │
│     groups      │1                │
│─────────────────│◄────────────────┘
│ id (PK)         │
│ user_id (FK)    │
│ name            │
│ color           │
│ icon            │
│ created_at      │
└─────────────────┘

┌─────────────────┐         ┌─────────────────┐
│     shares      │*       1│     events      │
│─────────────────│────────►│─────────────────│
│ id (PK)         │         │ id (PK)         │
│ event_id (FK)   │         └─────────────────┘
│ sender_id (FK)  │
│ share_code      │         ┌─────────────────┐
│ share_type      │        1│     users       │
│ expires_at      │◄────────│─────────────────│
│ created_at      │         │ id (PK)         │
└─────────────────┘         └─────────────────┘

┌─────────────────┐
│  notifications  │
│─────────────────│
│ id (PK)         │
│ event_id (FK)   │
│ user_id (FK)    │
│ scheduled_at    │
│ sent_at         │
│ onesignal_id    │
│ status          │
│ created_at      │
└─────────────────┘

┌─────────────────┐
│  sync_logs      │
│─────────────────│
│ id (PK)         │
│ user_id (FK)    │
│ device_id       │
│ synced_at       │
│ status          │
└─────────────────┘
```

#### 4.1.2 Table Definitions

**users table:**
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE,
    phone VARCHAR(20) UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    google_id VARCHAR(255) UNIQUE,
    apple_id VARCHAR(255) UNIQUE,
    full_name VARCHAR(100),
    avatar_url TEXT,
    language VARCHAR(5) DEFAULT 'vi',
    timezone VARCHAR(50) DEFAULT 'Asia/Ho_Chi_Minh',
    is_verified BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    last_login_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    deleted_at TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_phone ON users(phone);
CREATE INDEX idx_users_google_id ON users(google_id);
```

**events table:**
```sql
CREATE TABLE events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    name VARCHAR(200) NOT NULL,
    type VARCHAR(50) NOT NULL, -- 'gio', 'sinh_nhat', 'le', 'ngay_cuoi', 'ky_niem', 'khac'
    calendar_type VARCHAR(10) NOT NULL, -- 'lunar', 'solar'
    
    -- Lunar date (if applicable)
    lunar_day INTEGER,
    lunar_month INTEGER,
    lunar_year INTEGER,
    lunar_is_leap BOOLEAN DEFAULT FALSE,
    lunar_leap_option VARCHAR(10), -- 'first', 'second', 'both'
    
    -- Solar date (calculated or direct input)
    solar_date DATE NOT NULL,
    
    -- Recurrence
    is_recurring BOOLEAN DEFAULT TRUE,
    recurrence_end_date DATE,
    
    -- Notes
    notes TEXT,
    location VARCHAR(255),
    
    -- Notifications
    notify_enabled BOOLEAN DEFAULT TRUE,
    notify_days_before INTEGER DEFAULT 3,
    notify_times_per_day INTEGER DEFAULT 4,
    notify_time_slots JSON, -- [{"hour": 6, "minute": 30}, ...]
    
    -- Metadata
    color VARCHAR(20),
    icon VARCHAR(50),
    
    -- Timestamps
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    deleted_at TIMESTAMP
);

CREATE INDEX idx_events_user_id ON events(user_id);
CREATE INDEX idx_events_solar_date ON events(solar_date);
CREATE INDEX idx_events_type ON events(type);
CREATE INDEX idx_events_notify_enabled ON events(notify_enabled);
```

**groups table:**
```sql
CREATE TABLE groups (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    color VARCHAR(20),
    icon VARCHAR(50),
    sort_order INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    deleted_at TIMESTAMP
);

CREATE INDEX idx_groups_user_id ON groups(user_id);
```

**event_groups table (Many-to-Many):**
```sql
CREATE TABLE event_groups (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    event_id UUID NOT NULL REFERENCES events(id) ON DELETE CASCADE,
    group_id UUID NOT NULL REFERENCES groups(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(event_id, group_id)
);

CREATE INDEX idx_event_groups_event_id ON event_groups(event_id);
CREATE INDEX idx_event_groups_group_id ON event_groups(group_id);
```

**shares table:**
```sql
CREATE TABLE shares (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    sender_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    share_code VARCHAR(50) UNIQUE NOT NULL,
    share_type VARCHAR(20) NOT NULL, -- 'event', 'group', 'all'
    entity_ids JSON, -- Array of event/group IDs
    expires_at TIMESTAMP,
    max_uses INTEGER DEFAULT NULL,
    current_uses INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_shares_share_code ON shares(share_code);
CREATE INDEX idx_shares_sender_id ON shares(sender_id);
```

**shared_events table (Track who received what):**
```sql
CREATE TABLE shared_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    share_id UUID NOT NULL REFERENCES shares(id) ON DELETE CASCADE,
    recipient_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    event_id UUID NOT NULL REFERENCES events(id) ON DELETE CASCADE,
    accepted_at TIMESTAMP DEFAULT NOW(),
    is_active BOOLEAN DEFAULT TRUE
);

CREATE INDEX idx_shared_events_recipient_id ON shared_events(recipient_id);
CREATE INDEX idx_shared_events_event_id ON shared_events(event_id);
```

**notifications table:**
```sql
CREATE TABLE notifications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    event_id UUID NOT NULL REFERENCES events(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    scheduled_at TIMESTAMP NOT NULL,
    sent_at TIMESTAMP,
    onesignal_notification_id VARCHAR(100),
    status VARCHAR(20) DEFAULT 'pending', -- 'pending', 'sent', 'failed', 'cancelled'
    error_message TEXT,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_notifications_scheduled_at ON notifications(scheduled_at);
CREATE INDEX idx_notifications_status ON notifications(status);
CREATE INDEX idx_notifications_user_id ON notifications(user_id);
```

**devices table (For push notifications):**
```sql
CREATE TABLE devices (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    device_token VARCHAR(255) NOT NULL,
    platform VARCHAR(20) NOT NULL, -- 'ios', 'android'
    onesignal_player_id VARCHAR(100) UNIQUE,
    app_version VARCHAR(20),
    os_version VARCHAR(20),
    is_active BOOLEAN DEFAULT TRUE,
    last_active_at TIMESTAMP DEFAULT NOW(),
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_devices_user_id ON devices(user_id);
CREATE INDEX idx_devices_onesignal_player_id ON devices(onesignal_player_id);
```

**sync_logs table:**
```sql
CREATE TABLE sync_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    device_id UUID REFERENCES devices(id) ON DELETE SET NULL,
    sync_type VARCHAR(20) NOT NULL, -- 'pull', 'push'
    entity_type VARCHAR(50), -- 'events', 'groups', 'all'
    status VARCHAR(20) DEFAULT 'success', -- 'success', 'failed', 'partial'
    items_synced INTEGER DEFAULT 0,
    error_message TEXT,
    synced_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_sync_logs_user_id ON sync_logs(user_id);
CREATE INDEX idx_sync_logs_synced_at ON sync_logs(synced_at);
```

### 4.2 SQLite Schema (Mobile Local Storage)

**Same structure as PostgreSQL but simplified:**

- Remove complex indexes (performance not critical)
- Use `INTEGER PRIMARY KEY AUTOINCREMENT` instead of UUID for local IDs
- Add `server_id` field to track server UUID
- Add `sync_status` field: 'synced', 'pending_upload', 'pending_delete'
- Add `last_modified` timestamp for sync

**Key tables:**
- `local_events`
- `local_groups`
- `local_event_groups`
- `local_sync_queue` (tracks pending changes)

### 4.3 Data Types & Constraints

**Event Types (enum):**
```typescript
enum EventType {
  GIO = 'gio',              // Giỗ/Kỵ
  SINH_NHAT = 'sinh_nhat',  // Sinh nhật
  LE = 'le',                // Lễ hội
  NGAY_CUOI = 'ngay_cuoi',  // Ngày cưới
  KY_NIEM = 'ky_niem',      // Kỷ niệm
  KHAC = 'khac'             // Khác
}
```

**Calendar Types:**
```typescript
enum CalendarType {
  LUNAR = 'lunar',
  SOLAR = 'solar'
}
```

**Notification Status:**
```typescript
enum NotificationStatus {
  PENDING = 'pending',
  SENT = 'sent',
  FAILED = 'failed',
  CANCELLED = 'cancelled'
}
```

---

## 5. API Design

### 5.1 API Structure

**Base URL:**
- Development: `http://localhost:3000/api/v1`
- Production (via Cloudflare): `https://your-tunnel.trycloudflare.com/api/v1`

**Authentication:**
- Header: `Authorization: Bearer <JWT_TOKEN>`
- Public endpoints: `/auth/*`

**Response Format:**
```json
{
  "success": true,
  "data": { ... },
  "message": "Success",
  "timestamp": "2026-02-02T10:30:00Z"
}
```

**Error Format:**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "email",
        "message": "Email is required"
      }
    ]
  },
  "timestamp": "2026-02-02T10:30:00Z"
}
```

### 5.2 API Endpoints

#### 5.2.1 Authentication

**POST /auth/register**
```
Request:
{
  "email": "user@example.com",
  "password": "SecurePass123!",
  "phone": "+84901234567" (optional),
  "full_name": "Nguyen Van A"
}

Response:
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "full_name": "Nguyen Van A"
    },
    "access_token": "jwt_token",
    "refresh_token": "refresh_token"
  }
}
```

**POST /auth/login**
```
Request:
{
  "email": "user@example.com",
  "password": "SecurePass123!"
}

Response: Same as register
```

**POST /auth/login/google**
```
Request:
{
  "google_token": "google_oauth_token"
}

Response: Same as register
```

**POST /auth/login/phone**
```
Request:
{
  "phone": "+84901234567",
  "otp": "123456"
}

Response: Same as register
```

**POST /auth/refresh**
```
Request:
{
  "refresh_token": "refresh_token"
}

Response:
{
  "access_token": "new_jwt_token"
}
```

**POST /auth/logout**
```
Headers: Authorization: Bearer <token>

Response:
{
  "success": true,
  "message": "Logged out successfully"
}
```

#### 5.2.2 Events

**GET /events**
```
Query params:
- page: number (default: 1)
- limit: number (default: 20)
- type: EventType (optional)
- group_id: uuid (optional)
- from_date: date (optional)
- to_date: date (optional)
- search: string (optional)

Response:
{
  "success": true,
  "data": {
    "events": [
      {
        "id": "uuid",
        "name": "Giỗ ông nội",
        "type": "gio",
        "calendar_type": "lunar",
        "lunar_date": { "day": 15, "month": 3, "year": null },
        "solar_date": "2026-04-18",
        "notes": "Cúng tại nhà",
        "notify_enabled": true,
        "groups": [
          { "id": "uuid", "name": "Gia đình nội", "color": "#FF5722" }
        ],
        "countdown_days": 75,
        "created_at": "2026-01-01T00:00:00Z",
        "updated_at": "2026-01-01T00:00:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 50,
      "total_pages": 3
    }
  }
}
```

**GET /events/:id**
```
Response:
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Giỗ ông nội",
    ... (full event details)
  }
}
```

**POST /events**
```
Request:
{
  "name": "Giỗ ông nội",
  "type": "gio",
  "calendar_type": "lunar",
  "lunar_date": {
    "day": 15,
    "month": 3,
    "is_leap": false,
    "leap_option": null
  },
  "notes": "Cúng tại nhà",
  "notify_enabled": true,
  "notify_days_before": 3,
  "group_ids": ["uuid1", "uuid2"]
}

Response:
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Giỗ ông nội",
    "solar_date": "2026-04-18", // Auto-calculated
    ...
  }
}
```

**PUT /events/:id**
```
Request: Same as POST (partial update allowed)

Response: Updated event
```

**DELETE /events/:id**
```
Response:
{
  "success": true,
  "message": "Event deleted successfully"
}
```

**POST /events/bulk**
```
Request:
{
  "events": [
    { ... event1 },
    { ... event2 }
  ]
}

Response:
{
  "success": true,
  "data": {
    "created": 2,
    "failed": 0,
    "events": [ ... ]
  }
}
```

#### 5.2.3 Groups

**GET /groups**
```
Response:
{
  "success": true,
  "data": {
    "groups": [
      {
        "id": "uuid",
        "name": "Gia đình nội",
        "color": "#FF5722",
        "icon": "family",
        "event_count": 5
      }
    ]
  }
}
```

**POST /groups**
```
Request:
{
  "name": "Gia đình nội",
  "color": "#FF5722",
  "icon": "family",
  "description": "Các ngày giỗ gia đình nội"
}

Response: Created group
```

**PUT /groups/:id**
```
Request: Same as POST

Response: Updated group
```

**DELETE /groups/:id**
```
Response:
{
  "success": true,
  "message": "Group deleted. Events remain."
}
```

#### 5.2.4 Sharing

**POST /shares**
```
Request:
{
  "share_type": "group",
  "entity_ids": ["group_uuid"],
  "expires_in_days": 7 (optional)
}

Response:
{
  "success": true,
  "data": {
    "share_code": "ABC123XYZ",
    "share_url": "https://lunar-app.link/share/ABC123XYZ",
    "expires_at": "2026-02-09T00:00:00Z"
  }
}
```

**GET /shares/:share_code**
```
Response:
{
  "success": true,
  "data": {
    "share_type": "group",
    "sender": {
      "name": "Nguyen Van A",
      "avatar": "url"
    },
    "preview": {
      "group_name": "Gia đình nội",
      "event_count": 5,
      "events": [
        { "name": "Giỗ ông nội", "date": "2026-04-18" }
      ]
    }
  }
}
```

**POST /shares/:share_code/accept**
```
Headers: Authorization required

Response:
{
  "success": true,
  "data": {
    "imported_events": 5,
    "message": "Events added to your calendar"
  }
}
```

#### 5.2.5 Notifications

**GET /notifications/upcoming**
```
Response:
{
  "success": true,
  "data": {
    "notifications": [
      {
        "id": "uuid",
        "event": { ... },
        "scheduled_at": "2026-02-03T06:30:00Z",
        "status": "pending"
      }
    ]
  }
}
```

**POST /notifications/test**
```
Request:
{
  "event_id": "uuid"
}

Response:
{
  "success": true,
  "message": "Test notification sent"
}
```

**PUT /notifications/settings**
```
Request:
{
  "global_enabled": true,
  "time_slots": [
    { "hour": 6, "minute": 30 },
    { "hour": 11, "minute": 45 },
    { "hour": 18, "minute": 30 },
    { "hour": 20, "minute": 30 }
  ]
}

Response:
{
  "success": true,
  "message": "Settings updated"
}
```

#### 5.2.6 Sync

**POST /sync**
```
Request:
{
  "device_id": "uuid",
  "last_sync_time": "2026-02-01T00:00:00Z",
  "changes": {
    "events": {
      "created": [ ... ],
      "updated": [ ... ],
      "deleted": ["uuid1", "uuid2"]
    },
    "groups": {
      "created": [ ... ],
      "updated": [ ... ],
      "deleted": [ ... ]
    }
  }
}

Response:
{
  "success": true,
  "data": {
    "server_changes": {
      "events": {
        "created": [ ... ],
        "updated": [ ... ],
        "deleted": [ ... ]
      },
      "groups": { ... }
    },
    "sync_time": "2026-02-02T10:30:00Z"
  }
}
```

**GET /sync/status**
```
Response:
{
  "success": true,
  "data": {
    "last_sync": "2026-02-02T10:30:00Z",
    "pending_changes": 0,
    "conflicts": []
  }
}
```

#### 5.2.7 Calendar Export

**POST /calendar/export/google**
```
Request:
{
  "year": 2027,
  "event_ids": ["uuid1", "uuid2"] (optional, default: all)
}

Response:
{
  "success": true,
  "data": {
    "exported_count": 10,
    "google_calendar_url": "https://calendar.google.com/..."
  }
}
```

#### 5.2.8 Lunar Calendar

**POST /lunar/convert**
```
Request:
{
  "lunar_date": {
    "day": 15,
    "month": 3,
    "year": 2026,
    "is_leap": false
  }
}

Response:
{
  "success": true,
  "data": {
    "solar_date": "2026-04-18",
    "year_info": {
      "is_leap_year": false,
      "leap_month": null
    }
  }
}
```

**POST /lunar/year-info**
```
Request:
{
  "year": 2026
}

Response:
{
  "success": true,
  "data": {
    "year": 2026,
    "is_leap_year": false,
    "leap_month": null,
    "lunar_new_year": "2026-02-17",
    "tet_name": "Bính Ngọ"
  }
}
```

#### 5.2.9 User Management

**GET /user/profile**
```
Response:
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "user@example.com",
    "full_name": "Nguyen Van A",
    "avatar_url": "url",
    "language": "vi",
    "timezone": "Asia/Ho_Chi_Minh",
    "created_at": "2026-01-01T00:00:00Z"
  }
}
```

**PUT /user/profile**
```
Request:
{
  "full_name": "Nguyen Van A",
  "language": "en",
  "avatar_url": "new_url"
}

Response: Updated profile
```

**POST /user/change-password**
```
Request:
{
  "current_password": "old",
  "new_password": "new"
}

Response:
{
  "success": true,
  "message": "Password changed"
}
```

**DELETE /user/account**
```
Request:
{
  "password": "confirm_password"
}

Response:
{
  "success": true,
  "message": "Account deleted"
}
```

### 5.3 Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `VALIDATION_ERROR` | 400 | Invalid input data |
| `UNAUTHORIZED` | 401 | Missing or invalid token |
| `FORBIDDEN` | 403 | No permission |
| `NOT_FOUND` | 404 | Resource not found |
| `CONFLICT` | 409 | Duplicate resource |
| `RATE_LIMIT` | 429 | Too many requests |
| `SERVER_ERROR` | 500 | Internal server error |
| `SERVICE_UNAVAILABLE` | 503 | Service temporarily down |

---

## 6. Frontend Architecture

### 6.1 Folder Structure

```
frontend/
├── src/
│   ├── components/           # Reusable UI components
│   │   ├── common/
│   │   │   ├── Button/
│   │   │   ├── Input/
│   │   │   ├── Card/
│   │   │   └── ...
│   │   ├── event/
│   │   │   ├── EventCard/
│   │   │   ├── EventForm/
│   │   │   └── EventList/
│   │   └── calendar/
│   │       ├── LunarCalendar/
│   │       ├── DatePicker/
│   │       └── MonthView/
│   ├── screens/             # Screen components
│   │   ├── Auth/
│   │   │   ├── LoginScreen.tsx
│   │   │   ├── RegisterScreen.tsx
│   │   │   └── OnboardingScreen.tsx
│   │   ├── Home/
│   │   │   ├── HomeScreen.tsx
│   │   │   └── EventDetailScreen.tsx
│   │   ├── Event/
│   │   │   ├── CreateEventScreen.tsx
│   │   │   ├── EditEventScreen.tsx
│   │   │   └── EventListScreen.tsx
│   │   ├── Group/
│   │   │   ├── GroupListScreen.tsx
│   │   │   └── GroupDetailScreen.tsx
│   │   ├── Calendar/
│   │   │   └── CalendarViewScreen.tsx
│   │   └── Settings/
│   │       ├── SettingsScreen.tsx
│   │       ├── NotificationSettingsScreen.tsx
│   │       └── ProfileScreen.tsx
│   ├── navigation/          # Navigation setup
│   │   ├── RootNavigator.tsx
│   │   ├── AuthNavigator.tsx
│   │   ├── MainNavigator.tsx
│   │   └── types.ts
│   ├── store/               # Redux store
│   │   ├── index.ts
│   │   ├── slices/
│   │   │   ├── authSlice.ts
│   │   │   ├── eventSlice.ts
│   │   │   ├── groupSlice.ts
│   │   │   └── notificationSlice.ts
│   │   └── api/            # RTK Query (optional)
│   │       └── apiSlice.ts
│   ├── services/           # API services
│   │   ├── api.ts         # Axios config
│   │   ├── authService.ts
│   │   ├── eventService.ts
│   │   ├── groupService.ts
│   │   └── syncService.ts
│   ├── database/           # Local database
│   │   ├── index.ts
│   │   ├── schema.ts
│   │   ├── migrations/
│   │   └── queries/
│   ├── utils/              # Utilities
│   │   ├── lunarCalendar.ts
│   │   ├── dateHelpers.ts
│   │   ├── validators.ts
│   │   └── storage.ts
│   ├── hooks/              # Custom hooks
│   │   ├── useAuth.ts
│   │   ├── useEvents.ts
│   │   ├── useLunarCalendar.ts
│   │   └── useSync.ts
│   ├── constants/          # Constants
│   │   ├── colors.ts
│   │   ├── eventTypes.ts
│   │   └── config.ts
│   ├── types/              # TypeScript types
│   │   ├── event.ts
│   │   ├── user.ts
│   │   └── api.ts
│   ├── assets/             # Static assets
│   │   ├── images/
│   │   ├── icons/
│   │   └── fonts/
│   ├── i18n/               # Internationalization
│   │   ├── index.ts
│   │   ├── en.json
│   │   └── vi.json
│   └── App.tsx             # Root component
├── __tests__/              # Tests
├── android/                # Android native
├── ios/                    # iOS native
├── package.json
├── tsconfig.json
└── README.md
```

### 6.2 State Management

**Redux Store Structure:**
```typescript
{
  auth: {
    user: User | null,
    token: string | null,
    isAuthenticated: boolean,
    isLoading: boolean
  },
  events: {
    items: Event[],
    selectedEvent: Event | null,
    filters: EventFilters,
    isLoading: boolean,
    error: string | null
  },
  groups: {
    items: Group[],
    isLoading: boolean
  },
  notifications: {
    settings: NotificationSettings,
    upcoming: Notification[]
  },
  sync: {
    lastSyncTime: string | null,
    isSyncing: boolean,
    pendingChanges: number
  },
  ui: {
    theme: 'light' | 'dark',
    language: 'vi' | 'en'
  }
}
```

### 6.3 Offline Strategy

**Approach: Offline-First**

1. **Read Operations:**
   - Always read from local SQLite first
   - Display data immediately (fast)
   - Background sync from server
   - Update UI if server has newer data

2. **Write Operations:**
   - Save to local SQLite immediately
   - Mark as "pending_upload"
   - Queue sync task
   - Upload to server when online
   - Update local with server response

3. **Conflict Resolution:**
   - Last Write Wins (simple approach)
   - Server timestamp is authoritative
   - User can manually resolve if needed (future)

**Sync Queue:**
```typescript
interface SyncQueueItem {
  id: string;
  operation: 'create' | 'update' | 'delete';
  entity_type: 'event' | 'group';
  entity_id: string;
  data: any;
  timestamp: number;
  retry_count: number;
}
```

### 6.4 Component Architecture

**Container/Presenter Pattern:**

**Container (Smart Component):**
- Connects to Redux store
- Handles business logic
- Makes API calls
- Manages local state

**Presenter (Dumb Component):**
- Pure UI component
- Receives data via props
- Emits events via callbacks
- No business logic

**Example:**
```typescript
// Container
function EventListContainer() {
  const events = useSelector(selectEvents);
  const dispatch = useDispatch();
  
  const handleDelete = (id: string) => {
    dispatch(deleteEvent(id));
  };
  
  return <EventListPresenter events={events} onDelete={handleDelete} />;
}

// Presenter
function EventListPresenter({ events, onDelete }) {
  return (
    <FlatList
      data={events}
      renderItem={({ item }) => (
        <EventCard event={item} onDelete={() => onDelete(item.id)} />
      )}
    />
  );
}
```

---

## 7. Backend Architecture

### 7.1 NestJS Folder Structure

```
backend/
├── src/
│   ├── main.ts                    # Entry point
│   ├── app.module.ts              # Root module
│   ├── config/                    # Configuration
│   │   ├── database.config.ts
│   │   ├── jwt.config.ts
│   │   └── onesignal.config.ts
│   ├── modules/
│   │   ├── auth/                  # Authentication
│   │   │   ├── auth.module.ts
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── strategies/
│   │   │   │   ├── jwt.strategy.ts
│   │   │   │   └── google.strategy.ts
│   │   │   └── guards/
│   │   │       └── jwt-auth.guard.ts
│   │   ├── users/                 # User management
│   │   │   ├── users.module.ts
│   │   │   ├── users.controller.ts
│   │   │   ├── users.service.ts
│   │   │   ├── entities/
│   │   │   │   └── user.entity.ts
│   │   │   └── dto/
│   │   │       ├── create-user.dto.ts
│   │   │       └── update-user.dto.ts
│   │   ├── events/                # Event management
│   │   │   ├── events.module.ts
│   │   │   ├── events.controller.ts
│   │   │   ├── events.service.ts
│   │   │   ├── entities/
│   │   │   │   └── event.entity.ts
│   │   │   └── dto/
│   │   ├── groups/                # Group management
│   │   ├── notifications/         # Notification system
│   │   │   ├── notifications.module.ts
│   │   │   ├── notifications.service.ts
│   │   │   ├── schedulers/
│   │   │   │   └── notification.scheduler.ts
│   │   │   └── providers/
│   │   │       └── onesignal.provider.ts
│   │   ├── lunar-calendar/        # Lunar calendar
│   │   │   ├── lunar-calendar.module.ts
│   │   │   ├── lunar-calendar.service.ts
│   │   │   └── algorithms/
│   │   │       └── vietnamese-lunar.ts
│   │   ├── shares/                # Sharing
│   │   ├── sync/                  # Data sync
│   │   └── calendar-export/       # Calendar export
│   ├── common/                    # Shared utilities
│   │   ├── decorators/
│   │   ├── filters/
│   │   ├── interceptors/
│   │   ├── pipes/
│   │   └── utils/
│   └── database/                  # Database
│       ├── migrations/
│       └── seeds/
├── test/                          # E2E tests
├── package.json
├── tsconfig.json
└── README.md
```

### 7.2 Module Design

**Each module follows:**
- **Module:** Defines dependencies
- **Controller:** Handles HTTP requests
- **Service:** Business logic
- **Repository:** Database access (via TypeORM)
- **DTO:** Data Transfer Objects
- **Entity:** Database models

**Example: Events Module**
```typescript
// events.module.ts
@Module({
  imports: [
    TypeOrmModule.forFeature([Event, EventGroup]),
    LunarCalendarModule,
    NotificationsModule
  ],
  controllers: [EventsController],
  providers: [EventsService],
  exports: [EventsService]
})
export class EventsModule {}

// events.controller.ts
@Controller('events')
@UseGuards(JwtAuthGuard)
export class EventsController {
  constructor(private eventsService: EventsService) {}
  
  @Get()
  findAll(@Query() query: FindEventsDto) {
    return this.eventsService.findAll(query);
  }
  
  @Post()
  create(@Body() dto: CreateEventDto, @User() user: UserEntity) {
    return this.eventsService.create(dto, user.id);
  }
}

// events.service.ts
@Injectable()
export class EventsService {
  constructor(
    @InjectRepository(Event) private eventRepo: Repository<Event>,
    private lunarCalendarService: LunarCalendarService,
    private notificationsService: NotificationsService
  ) {}
  
  async create(dto: CreateEventDto, userId: string) {
    // Convert lunar to solar if needed
    if (dto.calendar_type === 'lunar') {
      dto.solar_date = await this.lunarCalendarService.convertToSolar(
        dto.lunar_date
      );
    }
    
    // Create event
    const event = this.eventRepo.create({ ...dto, user_id: userId });
    await this.eventRepo.save(event);
    
    // Schedule notifications
    await this.notificationsService.scheduleForEvent(event);
    
    return event;
  }
}
```

### 7.3 Scheduled Jobs

**Notification Scheduler:**
```typescript
@Injectable()
export class NotificationScheduler {
  constructor(
    @InjectRepository(Event) private eventRepo: Repository<Event>,
    @InjectRepository(Notification) private notifRepo: Repository<Notification>,
    private onesignalProvider: OneSignalProvider
  ) {}
  
  // Run every hour
  @Cron('0 * * * *')
  async checkUpcomingNotifications() {
    const now = new Date();
    const oneHourLater = new Date(now.getTime() + 60 * 60 * 1000);
    
    // Find notifications to send in next hour
    const notifications = await this.notifRepo.find({
      where: {
        scheduled_at: Between(now, oneHourLater),
        status: 'pending'
      },
      relations: ['event', 'user', 'user.devices']
    });
    
    // Send each notification
    for (const notif of notifications) {
      await this.sendNotification(notif);
    }
  }
  
  private async sendNotification(notif: Notification) {
    try {
      const result = await this.onesignalProvider.send({
        player_ids: notif.user.devices.map(d => d.onesignal_player_id),
        headings: { vi: `Còn ${notif.countdown_days} ngày đến ${notif.event.name}` },
        contents: { vi: `${notif.event.solar_date} (${notif.event.lunar_date})` },
        data: { event_id: notif.event.id }
      });
      
      notif.status = 'sent';
      notif.sent_at = new Date();
      notif.onesignal_notification_id = result.id;
      await this.notifRepo.save(notif);
    } catch (error) {
      notif.status = 'failed';
      notif.error_message = error.message;
      await this.notifRepo.save(notif);
    }
  }
}
```

---

## 8. Lunar Calendar Implementation

### 8.1 Vietnamese Lunar Calendar Algorithm

**Key Differences from Chinese:**
- Timezone: GMT+7 (Vietnam) vs GMT+8 (China)
- New moon calculation at 0:00 Hanoi time
- Can differ by 1 day from Chinese calendar

**Implementation Approach:**

**Option 1: Use existing library (Recommended)**
```bash
npm install lunar-calendar-vn
```

**Option 2: Custom implementation**
```typescript
// lunar-calendar.service.ts
import { Injectable } from '@nestjs/common';

@Injectable()
export class LunarCalendarService {
  private readonly TIMEZONE_OFFSET = 7; // GMT+7
  
  /**
   * Convert lunar date to solar date for a given year
   */
  convertToSolar(lunar: LunarDate, year: number): Date {
    // 1. Get new moon dates for the year
    const newMoons = this.calculateNewMoons(year);
    
    // 2. Identify lunar months
    const lunarMonths = this.identifyLunarMonths(newMoons, year);
    
    // 3. Find the target month
    let targetMonth = lunarMonths[lunar.month - 1];
    if (lunar.is_leap) {
      targetMonth = lunarMonths.find(m => m.month === lunar.month && m.is_leap);
    }
    
    // 4. Calculate solar date
    const solarDate = new Date(targetMonth.start_date);
    solarDate.setDate(solarDate.getDate() + lunar.day - 1);
    
    return solarDate;
  }
  
  /**
   * Convert solar date to lunar date
   */
  convertToLunar(solar: Date): LunarDate {
    const year = solar.getFullYear();
    const newMoons = this.calculateNewMoons(year);
    const lunarMonths = this.identifyLunarMonths(newMoons, year);
    
    // Find which lunar month this solar date falls into
    const month = lunarMonths.find(m => 
      solar >= m.start_date && solar < m.end_date
    );
    
    const day = Math.floor((solar.getTime() - month.start_date.getTime()) / (24 * 60 * 60 * 1000)) + 1;
    
    return {
      day,
      month: month.month,
      year: month.year,
      is_leap: month.is_leap
    };
  }
  
  /**
   * Calculate new moon dates using astronomical algorithms
   */
  private calculateNewMoons(year: number): Date[] {
    // Use astronomical algorithm (e.g., Meeus algorithm)
    // Adjust for GMT+7 timezone
    // Return array of new moon dates
    
    // This is complex - use library or port from existing code
    // Reference: https://www.informatik.uni-leipzig.de/~duc/amlich/
    return [];
  }
  
  /**
   * Identify lunar months and leap month
   */
  private identifyLunarMonths(newMoons: Date[], year: number) {
    const months = [];
    
    // Calculate solar terms (24 jiéqì)
    const solarTerms = this.calculateSolarTerms(year);
    
    // Identify which month is leap month
    // A lunar month without a major solar term is a leap month
    
    // This is complex - refer to algorithm documentation
    
    return months;
  }
  
  /**
   * Calculate 24 solar terms
   */
  private calculateSolarTerms(year: number): Date[] {
    // Each solar term is ~15 days apart
    // Start from Winter Solstice of previous year
    return [];
  }
  
  /**
   * Get year information (leap year, leap month)
   */
  getYearInfo(year: number): YearInfo {
    const newMoons = this.calculateNewMoons(year);
    const months = this.identifyLunarMonths(newMoons, year);
    
    const leapMonth = months.find(m => m.is_leap);
    
    return {
      year,
      is_leap_year: !!leapMonth,
      leap_month: leapMonth?.month || null,
      lunar_new_year: months[0].start_date,
      total_days: months.reduce((sum, m) => sum + m.days, 0)
    };
  }
}
```

**TypeScript Types:**
```typescript
interface LunarDate {
  day: number;      // 1-30
  month: number;    // 1-12
  year?: number;    // Optional for recurring events
  is_leap: boolean;
}

interface YearInfo {
  year: number;
  is_leap_year: boolean;
  leap_month: number | null;
  lunar_new_year: Date;
  total_days: number;
}

interface LunarMonth {
  month: number;
  year: number;
  is_leap: boolean;
  start_date: Date;
  end_date: Date;
  days: number;
}
```

### 8.2 Libraries to Research

**Existing Vietnamese Lunar Calendar Libraries:**
1. **`lunar-calendar-vn`** (npm)
2. **`amlich`** (GitHub: vanng822/amlich)
3. **Custom port from:** https://www.informatik.uni-leipzig.de/~duc/amlich/

**Evaluation Criteria:**
- Accuracy (must match official Vietnamese calendar)
- Performance
- Maintenance status
- TypeScript support
- License

---

## 9. Notification System

### 9.1 OneSignal Integration

**Setup:**
```bash
npm install onesignal-node
```

**Configuration:**
```typescript
// onesignal.config.ts
export const oneSignalConfig = {
  appId: process.env.ONESIGNAL_APP_ID,
  restApiKey: process.env.ONESIGNAL_REST_API_KEY,
  userAuthKey: process.env.ONESIGNAL_USER_AUTH_KEY
};
```

**Provider:**
```typescript
// onesignal.provider.ts
import * as OneSignal from 'onesignal-node';

@Injectable()
export class OneSignalProvider {
  private client: OneSignal.Client;
  
  constructor() {
    this.client = new OneSignal.Client(
      oneSignalConfig.appId,
      oneSignalConfig.restApiKey
    );
  }
  
  async send(notification: OneSignalNotification) {
    const result = await this.client.createNotification({
      include_player_ids: notification.player_ids,
      headings: notification.headings,
      contents: notification.contents,
      data: notification.data,
      ios_badgeType: 'Increase',
      ios_badgeCount: 1,
      android_channel_id: 'lunar-reminders'
    });
    
    return result.body;
  }
  
  async createDevice(deviceInfo: DeviceInfo) {
    return this.client.addDevice({
      device_type: deviceInfo.platform === 'ios' ? 0 : 1,
      identifier: deviceInfo.device_token,
      language: deviceInfo.language || 'vi',
      timezone: deviceInfo.timezone || 7 * 3600 // GMT+7 in seconds
    });
  }
}
```

### 9.2 Notification Scheduling Strategy

**Pre-calculation Approach:**

When event is created/updated:
1. Calculate all notification times for next year
2. Store in `notifications` table
3. Cron job checks every hour for pending notifications

**Example:**
```typescript
async scheduleForEvent(event: Event) {
  const notifications = [];
  const currentYear = new Date().getFullYear();
  
  // Get solar date for this year
  let solarDate: Date;
  if (event.calendar_type === 'lunar') {
    solarDate = await this.lunarCalendarService.convertToSolar(
      event.lunar_date,
      currentYear
    );
  } else {
    solarDate = new Date(event.solar_date);
    solarDate.setFullYear(currentYear);
  }
  
  // Calculate notification times based on event type
  const daysBefore = event.type === 'gio' ? 3 : 1;
  const timesPerDay = event.notify_times_per_day || 4;
  const timeSlots = event.notify_time_slots || DEFAULT_TIME_SLOTS;
  
  for (let i = daysBefore; i >= 0; i--) {
    const notifDate = new Date(solarDate);
    notifDate.setDate(notifDate.getDate() - i);
    
    // Determine how many notifications for this day
    let slots = timeSlots.slice(0, timesPerDay);
    if (i === daysBefore) {
      slots = timeSlots.slice(0, 2); // First day: 2 times
    } else if (i > 0) {
      slots = timeSlots.slice(0, 4); // Middle days: 4 times
    }
    
    for (const slot of slots) {
      const scheduledAt = new Date(notifDate);
      scheduledAt.setHours(slot.hour, slot.minute, 0, 0);
      
      notifications.push({
        event_id: event.id,
        user_id: event.user_id,
        scheduled_at: scheduledAt,
        status: 'pending'
      });
    }
  }
  
  await this.notificationRepo.save(notifications);
}
```

### 9.3 Frontend Notification Handling

**React Native Setup:**
```typescript
// NotificationService.ts
import messaging from '@react-native-firebase/messaging';
import OneSignal from 'react-native-onesignal';

class NotificationService {
  async initialize() {
    // Request permissions
    const authStatus = await messaging().requestPermission();
    const enabled = authStatus === messaging.AuthorizationStatus.AUTHORIZED;
    
    if (enabled) {
      // Initialize OneSignal
      OneSignal.setAppId(ONESIGNAL_APP_ID);
      
      // Get device token
      const deviceToken = await messaging().getToken();
      
      // Register device with backend
      await api.registerDevice({
        device_token: deviceToken,
        platform: Platform.OS,
        onesignal_player_id: await OneSignal.getDeviceState().userId
      });
      
      // Listen for foreground notifications
      OneSignal.setNotificationOpenedHandler(this.handleNotificationOpened);
      
      // Listen for background notifications
      messaging().onNotificationOpenedApp(this.handleBackgroundNotification);
      
      // Listen for quit state
      messaging().getInitialNotification().then(this.handleQuitNotification);
    }
  }
  
  private handleNotificationOpened(openedEvent: OpenedEvent) {
    const { notification } = openedEvent;
    const eventId = notification.additionalData?.event_id;
    
    if (eventId) {
      // Navigate to event detail
      navigationRef.navigate('EventDetail', { id: eventId });
    }
  }
}
```

---

## 10. Data Synchronization

### 10.1 Sync Strategy

**Approach: Delta Sync with Timestamps**

**Principles:**
1. Each entity has `updated_at` timestamp
2. Client tracks `last_sync_time`
3. On sync: Send client changes + request server changes since `last_sync_time`
4. Conflict resolution: Last Write Wins (server timestamp authoritative)

### 10.2 Sync Flow

```
┌──────────────┐                           ┌──────────────┐
│   Client     │                           │   Server     │
└──────┬───────┘                           └──────┬───────┘
       │                                          │
       │ POST /sync                               │
       │ {                                        │
       │   last_sync_time: "2026-02-01T10:00",   │
       │   changes: {                             │
       │     events: {                            │
       │       created: [...],                    │
       │       updated: [...],                    │
       │       deleted: [ids]                     │
       │     }                                    │
       │   }                                      │
       │ }                                        │
       ├─────────────────────────────────────────►│
       │                                          │
       │                        Server processes: │
       │                 1. Validate + save client changes │
       │                 2. Find server changes since last_sync │
       │                 3. Detect conflicts                │
       │                                          │
       │                         Response:        │
       │                         {                │
       │                           server_changes: {...}, │
       │                           conflicts: [...],│
       │                           sync_time: "2026-02-02"│
       │                         }                │
       │◄─────────────────────────────────────────┤
       │                                          │
Client:│                                          │
1. Apply server_changes to local DB             │
2. Resolve conflicts (last write wins)           │
3. Update last_sync_time                         │
       │                                          │
```

### 10.3 Implementation

**Client Side:**
```typescript
// syncService.ts
class SyncService {
  async sync() {
    const lastSyncTime = await AsyncStorage.getItem('last_sync_time');
    
    // 1. Get local changes
    const localChanges = await this.getLocalChanges(lastSyncTime);
    
    // 2. Send to server
    const response = await api.post('/sync', {
      last_sync_time: lastSyncTime,
      changes: localChanges
    });
    
    // 3. Apply server changes
    await this.applyServerChanges(response.data.server_changes);
    
    // 4. Handle conflicts
    if (response.data.conflicts.length > 0) {
      await this.resolveConflicts(response.data.conflicts);
    }
    
    // 5. Update last sync time
    await AsyncStorage.setItem('last_sync_time', response.data.sync_time);
  }
  
  private async getLocalChanges(since: string) {
    const db = await getDatabase();
    
    const created = await db.executeSql(
      'SELECT * FROM local_events WHERE sync_status = ? AND created_at > ?',
      ['pending_upload', since]
    );
    
    const updated = await db.executeSql(
      'SELECT * FROM local_events WHERE sync_status = ? AND updated_at > ?',
      ['pending_upload', since]
    );
    
    const deleted = await db.executeSql(
      'SELECT server_id FROM local_events WHERE sync_status = ?',
      ['pending_delete']
    );
    
    return {
      events: {
        created: created.rows.raw(),
        updated: updated.rows.raw(),
        deleted: deleted.rows.raw().map(r => r.server_id)
      }
    };
  }
  
  private async applyServerChanges(changes: ServerChanges) {
    const db = await getDatabase();
    
    // Apply creations
    for (const event of changes.events.created) {
      await db.executeSql(
        'INSERT OR REPLACE INTO local_events (...) VALUES (...)',
        [event.id, ...]
      );
    }
    
    // Apply updates
    for (const event of changes.events.updated) {
      await db.executeSql(
        'UPDATE local_events SET ... WHERE server_id = ?',
        [..., event.id]
      );
    }
    
    // Apply deletions
    for (const id of changes.events.deleted) {
      await db.executeSql(
        'DELETE FROM local_events WHERE server_id = ?',
        [id]
      );
    }
  }
}
```

**Server Side:**
```typescript
// sync.service.ts
@Injectable()
export class SyncService {
  async sync(userId: string, syncDto: SyncDto) {
    // 1. Process client changes
    await this.processClientChanges(userId, syncDto.changes);
    
    // 2. Get server changes since last sync
    const serverChanges = await this.getServerChanges(
      userId,
      syncDto.last_sync_time
    );
    
    // 3. Detect conflicts
    const conflicts = await this.detectConflicts(
      syncDto.changes,
      serverChanges
    );
    
    return {
      server_changes: serverChanges,
      conflicts,
      sync_time: new Date().toISOString()
    };
  }
  
  private async processClientChanges(userId: string, changes: Changes) {
    // Process created events
    for (const event of changes.events.created) {
      await this.eventRepo.save({
        ...event,
        user_id: userId
      });
    }
    
    // Process updated events
    for (const event of changes.events.updated) {
      await this.eventRepo.update(event.id, event);
    }
    
    // Process deleted events
    for (const id of changes.events.deleted) {
      await this.eventRepo.softDelete(id);
    }
  }
  
  private async getServerChanges(userId: string, since: string) {
    const sinceDate = new Date(since);
    
    const created = await this.eventRepo.find({
      where: {
        user_id: userId,
        created_at: MoreThan(sinceDate)
      }
    });
    
    const updated = await this.eventRepo.find({
      where: {
        user_id: userId,
        updated_at: MoreThan(sinceDate),
        created_at: LessThanOrEqual(sinceDate)
      }
    });
    
    const deleted = await this.eventRepo.find({
      where: {
        user_id: userId,
        deleted_at: MoreThan(sinceDate)
      },
      withDeleted: true
    });
    
    return {
      events: {
        created,
        updated,
        deleted: deleted.map(e => e.id)
      }
    };
  }
}
```

---

## 11. Security

### 11.1 Authentication Flow

**JWT Token Structure:**
```json
{
  "sub": "user_uuid",
  "email": "user@example.com",
  "iat": 1234567890,
  "exp": 1234571490
}
```

**Token Lifecycle:**
- Access token: 15 minutes expiry
- Refresh token: 7 days expiry
- Stored securely: Keychain (iOS) / Keystore (Android)

**Implementation:**
```typescript
// jwt.strategy.ts
@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(private usersService: UsersService) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      secretOrKey: process.env.JWT_SECRET
    });
  }
  
  async validate(payload: JwtPayload) {
    const user = await this.usersService.findById(payload.sub);
    if (!user || !user.is_active) {
      throw new UnauthorizedException();
    }
    return user;
  }
}
```

### 11.2 API Security

**Rate Limiting:**
```typescript
// main.ts
import rateLimit from 'express-rate-limit';

app.use(
  rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // limit each IP to 100 requests per windowMs
    message: 'Too many requests from this IP'
  })
);
```

**Input Validation:**
```typescript
// create-event.dto.ts
export class CreateEventDto {
  @IsString()
  @IsNotEmpty()
  @MaxLength(200)
  name: string;
  
  @IsEnum(EventType)
  type: EventType;
  
  @IsEnum(CalendarType)
  calendar_type: CalendarType;
  
  @ValidateIf(o => o.calendar_type === 'lunar')
  @ValidateNested()
  @Type(() => LunarDateDto)
  lunar_date?: LunarDateDto;
  
  @IsDateString()
  solar_date: string;
}
```

**SQL Injection Prevention:**
- Use TypeORM parameterized queries
- Never concatenate user input into queries

**XSS Prevention:**
- Sanitize user input before storage
- Escape output in frontend

### 11.3 Data Encryption

**At Rest:**
- PostgreSQL: Use `pgcrypto` extension for sensitive fields
- Mobile: Use `react-native-encrypted-storage`

**In Transit:**
- HTTPS only (via Cloudflare Tunnel)
- TLS 1.3

**Password Hashing:**
```typescript
import * as bcrypt from 'bcrypt';

async hashPassword(password: string): Promise<string> {
  const salt = await bcrypt.genSalt(10);
  return bcrypt.hash(password, salt);
}

async comparePassword(password: string, hash: string): Promise<boolean> {
  return bcrypt.compare(password, hash);
}
```

---

## 12. Performance Optimization

### 12.1 Database Optimization

**Indexes:**
```sql
-- Already created in schema
CREATE INDEX idx_events_user_id ON events(user_id);
CREATE INDEX idx_events_solar_date ON events(solar_date);
CREATE INDEX idx_notifications_scheduled_at ON notifications(scheduled_at);
```

**Query Optimization:**
- Use `SELECT` with specific columns (not `SELECT *`)
- Use `LIMIT` for pagination
- Use `JOIN` appropriately
- Avoid N+1 queries (use eager loading)

**Example:**
```typescript
// BAD (N+1 query)
const events = await this.eventRepo.find({ user_id });
for (const event of events) {
  event.groups = await this.groupRepo.find({ event_id: event.id });
}

// GOOD (single query with join)
const events = await this.eventRepo.find({
  where: { user_id },
  relations: ['groups']
});
```

### 12.2 Caching Strategy

**Redis Caching:**
```typescript
// Cache user's events for 5 minutes
@Injectable()
export class EventsService {
  constructor(
    @InjectRepository(Event) private eventRepo: Repository<Event>,
    @Inject(CACHE_MANAGER) private cacheManager: Cache
  ) {}
  
  async findAll(userId: string): Promise<Event[]> {
    const cacheKey = `events:${userId}`;
    
    // Try cache first
    let events = await this.cacheManager.get<Event[]>(cacheKey);
    
    if (!events) {
      // Cache miss - query database
      events = await this.eventRepo.find({ where: { user_id: userId } });
      
      // Store in cache for 5 minutes
      await this.cacheManager.set(cacheKey, events, { ttl: 300 });
    }
    
    return events;
  }
  
  async create(dto: CreateEventDto, userId: string): Promise<Event> {
    const event = await this.eventRepo.save({ ...dto, user_id: userId });
    
    // Invalidate cache
    await this.cacheManager.del(`events:${userId}`);
    
    return event;
  }
}
```

**What to Cache:**
- User's events list
- Lunar calendar conversion results
- API responses (for read-heavy endpoints)

**What NOT to Cache:**
- Real-time data (notifications)
- Frequently changing data
- User-specific sensitive data

### 12.3 Frontend Optimization

**React Native Performance:**

1. **Use FlatList/SectionList for long lists**
```typescript
<FlatList
  data={events}
  renderItem={({ item }) => <EventCard event={item} />}
  keyExtractor={item => item.id}
  getItemLayout={(data, index) => ({
    length: ITEM_HEIGHT,
    offset: ITEM_HEIGHT * index,
    index
  })}
  removeClippedSubviews={true}
  maxToRenderPerBatch={10}
  windowSize={5}
/>
```

2. **Memoize expensive components**
```typescript
const EventCard = React.memo(({ event, onPress }) => {
  return (
    <TouchableOpacity onPress={() => onPress(event.id)}>
      <Text>{event.name}</Text>
      <Text>{event.solar_date}</Text>
    </TouchableOpacity>
  );
}, (prevProps, nextProps) => {
  return prevProps.event.id === nextProps.event.id &&
         prevProps.event.updated_at === nextProps.event.updated_at;
});
```

3. **Use useMemo/useCallback**
```typescript
const EventList = ({ events }) => {
  const sortedEvents = useMemo(() => {
    return events.sort((a, b) => 
      new Date(a.solar_date) - new Date(b.solar_date)
    );
  }, [events]);
  
  const handlePress = useCallback((id) => {
    navigation.navigate('EventDetail', { id });
  }, [navigation]);
  
  return <FlatList data={sortedEvents} />;
};
```

4. **Image Optimization**
- Use `resizeMode="cover"`
- Cache images with `react-native-fast-image`
- Compress images before upload

5. **Bundle Optimization**
- Code splitting (lazy load screens)
- Remove console.logs in production
- Minimize dependencies

---

## 13. Deployment Architecture

### 13.1 Local Development Setup

**System Requirements:**
- OS: macOS, Linux, or Windows (with WSL2)
- CPU: 4+ cores
- RAM: 8GB minimum, 16GB recommended
- Storage: 50GB+ SSD
- Network: Stable broadband 20Mbps+

**Services to Run:**
```bash
# PostgreSQL
docker run -d \
  --name lunar-postgres \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=lunar_reminder \
  -p 5432:5432 \
  -v postgres-data:/var/lib/postgresql/data \
  postgres:14

# Redis (optional)
docker run -d \
  --name lunar-redis \
  -p 6379:6379 \
  redis:7

# Backend API
cd backend
npm run start:dev

# Cloudflare Tunnel
cloudflared tunnel run lunar-reminder-api
```

### 13.2 Cloudflare Tunnel Setup

**Installation:**
```bash
# macOS
brew install cloudflare/cloudflare/cloudflared

# Linux
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb

# Windows
# Download from https://github.com/cloudflare/cloudflared/releases
```

**Configuration:**
```bash
# 1. Authenticate
cloudflared tunnel login

# 2. Create tunnel
cloudflared tunnel create lunar-reminder-api

# 3. Create config file
# ~/.cloudflared/config.yml
tunnel: <TUNNEL_ID>
credentials-file: /path/to/<TUNNEL_ID>.json

ingress:
  - hostname: lunar-api.yourdomain.com
    service: http://localhost:3000
  - service: http_status:404
```

**Run Tunnel:**
```bash
# Foreground
cloudflared tunnel run lunar-reminder-api

# Background (systemd on Linux)
sudo cloudflared service install
sudo systemctl start cloudflared
sudo systemctl enable cloudflared
```

**DNS Setup:**
```bash
# Point your domain to the tunnel
cloudflared tunnel route dns lunar-reminder-api lunar-api.yourdomain.com
```

### 13.3 Future Cloud Migration

**When to Migrate:**
- Users > 5,000
- Traffic > 1,000 requests/hour
- Need 99.9%+ uptime
- International expansion

**Recommended: Railway.app**

**Pros:**
- Git-based deployment
- Built-in PostgreSQL
- Easy to set up
- Affordable ($20-50/month)

**Migration Steps:**
1. Create Railway account
2. Connect GitHub repo
3. Add PostgreSQL database
4. Set environment variables
5. Deploy backend
6. Update frontend API URL
7. Test thoroughly
8. Switch DNS

**Alternative: AWS**

**Architecture:**
```
Route 53 (DNS) → CloudFront (CDN) → ALB (Load Balancer)
  → ECS/Fargate (Docker containers) → RDS (PostgreSQL)
  → ElastiCache (Redis) → S3 (File storage)
```

**Cost Estimate:**
- $100-300/month for 10K users
- $500-1000/month for 100K users

---

## 14. Development Environment

### 14.1 Required Tools

**General:**
- Git
- Node.js 18+ LTS
- npm or yarn
- VS Code (recommended)
- Postman or Insomnia (API testing)

**Mobile Development:**
- React Native CLI
- Android Studio (for Android)
- Xcode (for iOS, macOS only)
- Java JDK 11+

**Backend Development:**
- PostgreSQL client (pgAdmin, TablePlus, DBeaver)
- Redis client (RedisInsight, Medis)
- Docker & Docker Compose

### 14.2 VS Code Extensions

**Essential:**
- ESLint
- Prettier
- TypeScript
- GitLens
- Thunder Client (API testing)

**React Native:**
- React Native Tools
- ES7+ React/Redux/React-Native snippets

**Backend:**
- NestJS Snippets
- PostgreSQL

### 14.3 Git Workflow

**Branch Strategy:**
```
main (production)
  └── develop (development)
        ├── feature/TASK-XXX-feature-name
        ├── bugfix/TASK-XXX-bug-description
        └── hotfix/critical-bug
```

**Commit Message Convention:**
```
type(scope): subject

feat(events): add lunar calendar conversion
fix(auth): resolve token expiration issue
docs(readme): update setup instructions
test(events): add unit tests for event creation
chore(deps): update dependencies
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Code style (formatting)
- `refactor`: Code refactoring
- `test`: Tests
- `chore`: Maintenance tasks

### 14.4 Environment Variables

**Backend (.env):**
```env
# Server
NODE_ENV=development
PORT=3000

# Database
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_USER=postgres
DATABASE_PASSWORD=password
DATABASE_NAME=lunar_reminder

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRES_IN=15m
REFRESH_TOKEN_EXPIRES_IN=7d

# OneSignal
ONESIGNAL_APP_ID=your-app-id
ONESIGNAL_REST_API_KEY=your-api-key

# Google OAuth (optional)
GOOGLE_CLIENT_ID=your-client-id
GOOGLE_CLIENT_SECRET=your-client-secret

# Cloudflare Tunnel
TUNNEL_URL=https://lunar-api.yourdomain.com
```

**Frontend (.env):**
```env
API_URL=http://localhost:3000/api/v1
ONESIGNAL_APP_ID=your-app-id
GOOGLE_CLIENT_ID=your-client-id
```

---

## Appendix

### A. Technology Alternatives Considered

**Frontend:**
- Flutter: Considered but React Native chosen for JS ecosystem
- Native (Swift/Kotlin): Too much duplication

**Backend:**
- Express.js: Too minimal, chose NestJS for structure
- FastAPI (Python): Considered but TypeScript preferred

**Database:**
- MongoDB: Considered but relational structure fits better
- MySQL: PostgreSQL chosen for JSON support

**State Management:**
- MobX: Considered but Redux Toolkit more popular
- Context API: Not sufficient for complex state

### B. Performance Benchmarks (Target)

**API Response Times:**
- GET /events: < 200ms
- POST /events: < 500ms
- POST /sync: < 1000ms

**Mobile App:**
- App launch: < 2s
- Screen transition: < 300ms
- Event creation: < 1s

**Database:**
- Query execution: < 100ms (simple)
- Query execution: < 500ms (complex with joins)

### C. Scalability Targets

**Phase 1 (Local):**
- Users: 1,000 - 5,000
- Concurrent requests: 10-50
- Database size: < 1GB

**Phase 2 (Cloud):**
- Users: 10,000 - 100,000
- Concurrent requests: 100-500
- Database size: < 10GB

**Phase 3 (Scale):**
- Users: 100,000+
- Concurrent requests: 1,000+
- Database size: 10GB+
- Microservices architecture
- Horizontal scaling

---

**End of Technical Specification**

**Next Document:** UI/UX Specification

