# Architecture Design Principles

**Related Documentation:**
- [System Overview](./system-overview.md)
- [Tech Stack](./tech-stack.md)
- [Frontend Architecture](./frontend-architecture.md)
- [Backend Architecture](./backend-architecture.md)

---

## Architecture Principles

### 1. Offline-First

- App fully functional offline
- Local database (SQLite/Realm)
- Background sync when online

### 2. Microservices-Ready

- Modular NestJS architecture
- Easy to split into microservices later
- Independent module deployment

### 3. Scalability

- Horizontal scaling ready
- Database connection pooling
- Caching strategy

### 4. Security

- JWT-based authentication
- HTTPS only
- Input validation
- SQL injection prevention

### 5. Maintainability

- TypeScript throughout
- Clean architecture
- Comprehensive testing
- Clear documentation

---

## Design Patterns

### Frontend

- **Redux Pattern** for state management
- **Container/Presenter pattern** for components
- **HOC (Higher-Order Components)** for reusable logic
- **Custom Hooks** for business logic

### Backend

- **Repository Pattern** (TypeORM)
- **Service Layer Pattern**
- **Dependency Injection** (NestJS)
- **Strategy Pattern** (notification scheduling)

---

## Data Flow

### Event Creation Flow

```
User Input → Redux Action → Local Save (SQLite)
  → API Call → Backend Validation → PostgreSQL Save
  → Schedule Notifications → Response → Update Redux → UI Update
```

### Notification Flow

```
Scheduled Job (Cron) → Check Upcoming Events → Calculate Recipients
  → Create OneSignal Notifications → Send → Log Delivery
```

### Sync Flow

```
App Launch → Check Network → API Call (GET /sync?lastSyncTime=X)
  → Server: Find Changes → Response with Delta → Merge Local + Remote
  → Conflict Resolution (Last Write Wins) → Update Local DB
```
