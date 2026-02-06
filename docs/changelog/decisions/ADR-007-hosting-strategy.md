# ADR-007: Hosting Strategy

**Date:** 2026-02-02
**Status:** Approved

## Context

The backend API needs to be accessible to mobile clients. We needed to decide on a hosting strategy that balances cost, complexity, and scalability for the MVP phase.

## Decision

We chose to start with a **local server + Cloudflare Tunnel** setup, with a plan to migrate to cloud hosting as the user base grows.

## Rationale

- **Zero cost initially:** No cloud hosting fees during MVP development and early adoption
- **Fast development:** Quick setup without cloud provider complexity
- **Good for 1-5K users:** Sufficient capacity for initial user base
- **Easy to migrate to cloud later:** Application architecture remains cloud-ready
- **Cloudflare benefits:** Free SSL, DDoS protection, caching

## Alternatives Considered

### AWS/GCP Immediately
- **Pros:** Production-ready, scalable, professional infrastructure
- **Cons:** Overkill for MVP, ongoing costs before revenue, complex setup

### Railway/Render
- **Pros:** Simple deployment, reasonable free tier
- **Cons:** Save for when scale is needed, some limitations on free tier

### DigitalOcean Droplet
- **Pros:** Simple VPS, predictable pricing
- **Cons:** Still has monthly cost, requires server management

### Vercel/Netlify (Serverless)
- **Pros:** Free tier, automatic scaling
- **Cons:** NestJS not ideal for serverless, cold start issues

## Consequences

### Positive
- Zero infrastructure cost during development
- Full control over server environment
- Quick iteration and deployment
- Cloudflare Tunnel provides secure public access
- Can develop and test with production-like setup

### Negative
- Dependent on local machine/network availability
- Not suitable for production with paying users
- Limited scalability
- No automatic failover or redundancy

### Mitigations
- Design application to be cloud-agnostic
- Document cloud migration path
- Plan cloud migration before significant user growth
- Set up monitoring to detect when migration is needed
- Consider Railway or DigitalOcean as first cloud step

### Migration Triggers
- User count exceeds 1,000 active users
- Reliability becomes critical (paid features)
- Team availability for 24/7 uptime decreases
