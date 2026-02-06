# ADR-009: Offline-First Approach

**Date:** 2026-02-02
**Status:** Approved

## Context

Vietnamese users, including those in rural areas, may have intermittent internet connectivity. The app must function reliably regardless of network status, and users should be able to access their events at any time.

## Decision

We chose an **Offline-First Architecture** where all data is stored locally and synced to the server when online.

## Rationale

- **Better UX (fast, always works):** Local data access provides instant response times
- **Users in rural areas:** Many Vietnamese users have unreliable internet
- **Sync in background when online:** Users don't need to wait for network operations
- **Core functionality always available:** View and manage events without internet

## Alternatives Considered

### Online-First with Caching
- **Pros:** Simpler sync logic, always fresh data
- **Cons:** Poor experience with slow or no internet, users would be blocked

### Hybrid (Online when possible, limited offline)
- **Pros:** Balanced approach, less sync complexity
- **Cons:** Inconsistent user experience, confusing mental model

### Full Online (No offline)
- **Pros:** Simplest implementation, no sync conflicts
- **Cons:** Unusable without internet, poor UX for target users

## Consequences

### Positive
- App works instantly on launch
- Users can create and view events without internet
- No loading spinners for local operations
- Resilient to network issues
- Better battery usage (no constant network checks)

### Negative
- Sync complexity increases significantly
- Need conflict resolution strategy
- Local database schema must be maintained
- More storage used on device
- Potential for data inconsistencies

### Mitigations
- Implement clear sync status indicators
- Use timestamp-based conflict resolution (last-write-wins with user notification)
- Design clear merge strategies for conflicts
- Provide manual sync trigger for users
- Show sync status in UI

### Sync Strategy
1. All changes written to local database first
2. Changes queued for sync when online
3. Background sync with exponential backoff
4. Conflict detection with user resolution options
5. Sync status visible in settings

### Conflict Resolution
- Last-write-wins for simple fields
- Merge for complex data (notifications)
- User prompt for significant conflicts
- Server timestamp as source of truth for ordering
