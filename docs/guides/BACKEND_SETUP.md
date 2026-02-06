# Backend Setup Guide

This guide walks you through setting up the NestJS backend for the Lunar Reminder App.

---

## Prerequisites

- **Node.js** 18+ (LTS recommended)
- **PostgreSQL** 14+
- **npm** or **pnpm**

---

## Setup Steps

### 1. Navigate to Backend Directory

```bash
cd backend
```

### 2. Create Environment File

```bash
cp .env.example .env
```

### 3. Configure Environment Variables

Edit `.env` and configure your database credentials:

```env
# Database
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_NAME=lunar_reminder
DATABASE_USER=your_username
DATABASE_PASSWORD=your_password

# JWT
JWT_SECRET=your-super-secret-key
JWT_EXPIRES_IN=7d

# OneSignal (Push Notifications)
ONESIGNAL_APP_ID=your-app-id
ONESIGNAL_API_KEY=your-api-key

# App
PORT=3000
NODE_ENV=development
```

### 4. Install Dependencies

```bash
npm install
```

### 5. Set Up Database

Create the database in PostgreSQL:

```sql
CREATE DATABASE lunar_reminder;
```

Run migrations:

```bash
npm run db:migrate
```

(Optional) Seed sample data:

```bash
npm run db:seed
```

### 6. Start Development Server

```bash
npm run start:dev
```

The API will be available at `http://localhost:3000`

---

## Available Commands

| Command | Description |
|---------|-------------|
| `npm run start:dev` | Start development server with hot reload |
| `npm run start:debug` | Start with debug mode |
| `npm run start:prod` | Start production server |
| `npm run build` | Build for production |
| `npm run test` | Run unit tests |
| `npm run test:watch` | Run tests in watch mode |
| `npm run test:cov` | Generate test coverage report |
| `npm run test:e2e` | Run end-to-end tests |
| `npm run lint` | Run ESLint |
| `npm run lint:fix` | Fix ESLint issues |
| `npm run db:migrate` | Run database migrations |
| `npm run db:migrate:generate` | Generate new migration |
| `npm run db:seed` | Seed database with sample data |

---

## Environment Variables Reference

### Database

| Variable | Description | Required |
|----------|-------------|----------|
| `DATABASE_HOST` | PostgreSQL host | Yes |
| `DATABASE_PORT` | PostgreSQL port | Yes |
| `DATABASE_NAME` | Database name | Yes |
| `DATABASE_USER` | Database username | Yes |
| `DATABASE_PASSWORD` | Database password | Yes |

### Authentication

| Variable | Description | Required |
|----------|-------------|----------|
| `JWT_SECRET` | Secret key for JWT signing | Yes |
| `JWT_EXPIRES_IN` | Token expiration time (e.g., `7d`, `24h`) | Yes |

### Push Notifications

| Variable | Description | Required |
|----------|-------------|----------|
| `ONESIGNAL_APP_ID` | OneSignal application ID | No* |
| `ONESIGNAL_API_KEY` | OneSignal REST API key | No* |

*Required for push notification functionality

### Application

| Variable | Description | Default |
|----------|-------------|---------|
| `PORT` | Server port | `3000` |
| `NODE_ENV` | Environment mode | `development` |

---

## API Documentation

Once the server is running, access Swagger API documentation at:

```
http://localhost:3000/api/docs
```

---

## Troubleshooting

### Database Connection Failed

1. Verify PostgreSQL is running
2. Check credentials in `.env`
3. Ensure database exists

### Migration Errors

```bash
# Reset migrations
npm run db:migrate:revert

# Re-run migrations
npm run db:migrate
```

### Port Already in Use

Change the `PORT` in `.env` or kill the existing process:

```bash
lsof -i :3000
kill -9 <PID>
```

---

## Project Structure

```
backend/src/
├── app.module.ts       # Root module
├── main.ts             # Entry point
├── common/             # Shared utilities, guards, decorators
├── config/             # Configuration module
├── modules/
│   ├── auth/           # Authentication (login, register, JWT)
│   ├── users/          # User management
│   ├── events/         # Event CRUD operations
│   ├── groups/         # Group management
│   ├── notifications/  # Push notification handling
│   └── sync/           # Data synchronization
└── database/
    ├── migrations/     # TypeORM migrations
    └── seeds/          # Database seeders
```

---

**See Also:**
- [Architecture](../architecture/index.md)
- [API Documentation](../api/index.md)
- [Data Model](../data-model/index.md)
