# ADR-002: Backend Framework

**Date:** 2026-02-02
**Status:** Approved

## Context

The app requires a backend API to handle user authentication, data synchronization, event management, and push notification coordination. We needed a robust, scalable framework that supports rapid development.

## Decision

We chose **NestJS** as the backend framework for the API server.

## Rationale

- **TypeScript-first:** Native TypeScript support enables type safety and better developer experience
- **Structured, scalable architecture:** Module-based architecture promotes clean code organization
- **Great for REST APIs:** Built-in support for RESTful API development with decorators
- **Good documentation & community:** Comprehensive docs and active community support
- **Consistency with frontend:** Both frontend and backend use TypeScript

## Alternatives Considered

### Express.js
- **Pros:** Minimal, flexible, widely used
- **Cons:** Too minimal, requires significant boilerplate for structured applications, lacks built-in patterns

### FastAPI (Python)
- **Pros:** Excellent performance, automatic documentation, async support
- **Cons:** Python instead of TypeScript breaks consistency with frontend, team would need to context-switch between languages

### Hapi.js
- **Pros:** Good for large applications, plugin system
- **Cons:** Smaller community than NestJS, less modern approach

## Consequences

### Positive
- TypeScript consistency across the entire stack
- Built-in dependency injection for testable code
- Decorators simplify route definitions and validation
- TypeORM integration for database operations
- Easy to structure the codebase as the project grows

### Negative
- Steeper learning curve than Express.js
- More opinionated framework structure
- Slightly more boilerplate than minimal frameworks

### Mitigations
- Follow NestJS best practices from the start
- Use module generators to scaffold new features
- Document architectural patterns for team reference
