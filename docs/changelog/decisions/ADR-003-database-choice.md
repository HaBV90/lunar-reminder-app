# ADR-003: Database Choice

**Date:** 2026-02-02
**Status:** Approved

## Context

The app needs a reliable database to store user accounts, events, family groups, notification preferences, and synchronization data. The data model involves relationships between users, events, and groups.

## Decision

We chose **PostgreSQL** as the primary database.

## Rationale

- **Relational structure fits data model:** Events, users, families, and groups have clear relationships
- **ACID compliance:** Ensures data integrity for critical event and user data
- **JSON support:** JSONB columns provide flexibility for notification settings and metadata
- **Proven, reliable:** Battle-tested in production environments worldwide
- **TypeORM support:** Excellent integration with NestJS via TypeORM

## Alternatives Considered

### MongoDB
- **Pros:** Flexible schema, good for rapid prototyping
- **Cons:** Document model not ideal for the relational nature of our data (users-events-groups), eventual consistency concerns

### MySQL
- **Pros:** Widely used, good performance, familiar
- **Cons:** PostgreSQL has better JSON support, which we need for flexible settings storage

### SQLite (for server)
- **Pros:** Simple, no separate server needed
- **Cons:** Not suitable for production multi-user workloads, concurrent access limitations

## Consequences

### Positive
- Strong data integrity with foreign key constraints
- Complex queries supported for calendar views and event filtering
- JSONB allows flexible storage for notification preferences
- Full-text search capabilities for event search
- Mature ecosystem with excellent tooling

### Negative
- Requires separate database server
- More complex setup than SQLite
- Needs proper connection pooling for performance

### Mitigations
- Use Docker for local development setup
- Configure connection pooling from the start
- Document migration procedures
- Plan for database backups
