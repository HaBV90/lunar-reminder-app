# ADR-008: Authentication

**Date:** 2026-02-02
**Status:** Approved

## Context

The app needs an authentication system for users who want to sync their events across devices and share events with family members. Authentication should be optional (offline-first approach) but available for enhanced features.

## Decision

We chose **Custom JWT authentication** with **Social login** (Google, Apple) as an option.

## Rationale

- **Full control over auth flow:** Can customize the experience for Vietnamese users
- **Social login for convenience:** Reduces friction for users who prefer not to create new credentials
- **JWT industry standard:** Stateless authentication, well-understood security model
- **Apple Sign-In required:** App Store requires Apple Sign-In if any social login is offered
- **No vendor lock-in:** Own the authentication infrastructure

## Alternatives Considered

### Firebase Authentication
- **Pros:** Quick setup, handles social providers, secure by default
- **Cons:** Vendor lock-in to Google ecosystem, less control over user data, pricing at scale

### Auth0
- **Pros:** Enterprise-grade, extensive features, good documentation
- **Cons:** Expensive at scale, overkill for MVP, external dependency for critical feature

### Clerk
- **Pros:** Modern, developer-friendly, good UI components
- **Cons:** Newer service, pricing concerns, less established

### Supabase Auth
- **Pros:** Open-source, PostgreSQL integration
- **Cons:** Ties authentication to Supabase platform

## Consequences

### Positive
- Complete control over user data and authentication flow
- Can implement Vietnamese phone number login in future
- JWT works well with offline-first architecture
- No per-user costs for authentication
- Social login reduces friction while maintaining control

### Negative
- More development effort for authentication system
- Security responsibility is on our implementation
- Need to handle token refresh, password reset, etc.

### Mitigations
- Follow OWASP security guidelines
- Use well-tested JWT libraries (passport-jwt)
- Implement proper token rotation and refresh
- Store tokens securely on device (secure storage)
- Plan for 2FA in future versions

### Implementation Details
- Access token: 15-minute expiry
- Refresh token: 7-day expiry
- Secure storage on device for tokens
- HTTPS only for all authentication endpoints
