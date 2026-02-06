# ADR-005: Local Database Mobile

**Date:** 2026-02-02
**Status:** Approved

## Context

The app follows an offline-first approach, requiring local storage of events and user data on the mobile device. This enables users to access their events without an internet connection.

## Decision

We chose **SQLite** as the local database for the mobile app.

## Rationale

- **Simple, lightweight:** Minimal overhead, well-suited for mobile devices
- **SQL queries familiar:** Team can use familiar SQL patterns for data operations
- **Good React Native support:** Mature libraries available (expo-sqlite, react-native-sqlite-storage)
- **File-based:** No separate process needed, data stored in a single file
- **Cross-platform:** Works on both iOS and Android

## Alternatives Considered

### Realm
- **Pros:** Better performance for complex queries, reactive architecture
- **Cons:** More complex setup, proprietary query language, heavier dependency

### AsyncStorage
- **Pros:** Built into React Native, simple key-value storage
- **Cons:** Not suitable for structured data queries, limited to simple data

### WatermelonDB
- **Pros:** Built on SQLite with better React Native integration, lazy loading
- **Cons:** Additional abstraction layer, less direct control

### MMKV
- **Pros:** Extremely fast key-value storage
- **Cons:** Not designed for relational data, no query support

## Consequences

### Positive
- Consistent SQL-based querying across server and client
- Easy to implement complex queries for calendar views
- Lightweight with minimal battery impact
- Well-documented migration patterns
- Can store thousands of events efficiently

### Negative
- Requires manual sync logic implementation
- No built-in reactive queries (need to implement manually)
- Schema migrations need careful handling

### Mitigations
- Design clear sync strategy with conflict resolution
- Implement proper schema versioning
- Create abstraction layer for database operations
- Use transactions for data integrity
