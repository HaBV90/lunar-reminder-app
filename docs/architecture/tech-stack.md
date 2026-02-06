# Technology Stack

**Related Documentation:**
- [System Overview](./system-overview.md)
- [Frontend Architecture](./frontend-architecture.md)
- [Backend Architecture](./backend-architecture.md)
- [Deployment](./deployment.md)

---

## Frontend (Mobile App)

### Core

- **React Native:** 0.73+ (latest stable)
- **TypeScript:** 5.0+
- **React:** 18+

### State Management

- **Redux Toolkit:** 2.0+
- **Redux Persist:** For offline state
- **RTK Query:** For API calls (optional)

### Navigation

- **React Navigation:** v6+
- Stack, Tab, Drawer navigators

### UI Components

- **Custom components** (built from scratch)
- **React Native Vector Icons:** For icons
- **Calendar Library:** TBD (research needed)
  - Options: `react-native-calendars`, `react-native-calendar-picker`

### Local Database

- **SQLite** via `react-native-sqlite-storage`
- Or **Realm** (alternative, better performance)
- **Recommendation:** SQLite for simplicity

### Utilities

- **Axios:** HTTP client
- **Day.js:** Date manipulation
- **React Hook Form:** Form handling
- **Yup:** Validation schemas

### Development

- **ESLint:** Linting
- **Prettier:** Code formatting
- **Jest:** Unit testing
- **React Native Testing Library:** Component testing
- **Detox:** E2E testing (optional)

---

## Backend (API Server)

### Core

- **NestJS:** 10+
- **TypeScript:** 5.0+
- **Node.js:** 18+ LTS

### Database

- **PostgreSQL:** 14+
- **TypeORM:** 0.3+ (ORM)
- **pg:** PostgreSQL driver

### Authentication

- **JWT:** `@nestjs/jwt`
- **Passport:** `@nestjs/passport`
- **bcrypt:** Password hashing

### Caching

- **Redis:** 7+
- **@nestjs/cache-manager**

### Task Scheduling

- **@nestjs/schedule:** Cron jobs
- **node-cron:** Advanced scheduling

### Validation

- **class-validator:** DTO validation
- **class-transformer:** Object transformation

### API Documentation

- **@nestjs/swagger:** OpenAPI spec generation

### Testing

- **Jest:** Unit testing
- **Supertest:** API testing

### Utilities

- **Moment.js** or **Day.js:** Date handling
- **Lunar Calendar Library:** TBD (research needed)
  - Options: Custom implementation, `lunar-calendar`, Vietnamese lunar libs

---

## Infrastructure

### Version Control

- **Git + GitHub**

### CI/CD

- **GitHub Actions**

### Monitoring (Future)

- **Sentry:** Error tracking
- **Datadog / Grafana:** Metrics

### Hosting (Current)

- **Local Machine:** Development + staging + "production"
- **Cloudflare Tunnel:** Expose to internet

### Hosting (Future - when scale)

- **Railway.app:** Quick deploy (recommended)
- **AWS:** Production-grade (EC2, RDS, S3)
- **Google Cloud:** Alternative

---

## External Services

### OneSignal

- Free tier: 10K subscribers
- Push notifications to iOS & Android
- REST API for sending
- Dashboard for analytics

### Google Calendar API

- OAuth 2.0 for user consent
- Create events programmatically
- Set reminders

### Cloudflare Tunnel

- Free
- No need for public IP
- Stable connection
- CLI tool: `cloudflared`
