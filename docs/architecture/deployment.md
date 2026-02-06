# Deployment Architecture

**Related Documentation:**
- [System Overview](./system-overview.md)
- [Tech Stack](./tech-stack.md)
- [Backend Architecture](./backend-architecture.md)

---

## Local Development Setup

### System Requirements

- **OS:** macOS, Linux, or Windows (with WSL2)
- **CPU:** 4+ cores
- **RAM:** 8GB minimum, 16GB recommended
- **Storage:** 50GB+ SSD
- **Network:** Stable broadband 20Mbps+

### Services to Run

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

---

## Cloudflare Tunnel Setup

### Installation

```bash
# macOS
brew install cloudflare/cloudflare/cloudflared

# Linux
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb

# Windows
# Download from https://github.com/cloudflare/cloudflared/releases
```

### Configuration

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

### Run Tunnel

```bash
# Foreground
cloudflared tunnel run lunar-reminder-api

# Background (systemd on Linux)
sudo cloudflared service install
sudo systemctl start cloudflared
sudo systemctl enable cloudflared
```

### DNS Setup

```bash
# Point your domain to the tunnel
cloudflared tunnel route dns lunar-reminder-api lunar-api.yourdomain.com
```

---

## Future Cloud Migration

### When to Migrate

- Users > 5,000
- Traffic > 1,000 requests/hour
- Need 99.9%+ uptime
- International expansion

### Recommended: Railway.app

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

### Alternative: AWS

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

## Development Environment

### Required Tools

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

### VS Code Extensions

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

### Git Workflow

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

### Environment Variables

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
