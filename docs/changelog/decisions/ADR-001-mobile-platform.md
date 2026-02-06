# ADR-001: Mobile Platform Choice

**Date:** 2026-02-02
**Status:** Approved

## Context

The Lunar Reminder App needs to be available on both iOS and Android platforms to reach the maximum number of Vietnamese users. We needed to decide between native development for each platform or a cross-platform approach.

## Decision

We chose **React Native** for cross-platform mobile app development.

## Rationale

- **Single codebase for iOS & Android:** Reduces development time and maintenance effort significantly
- **JavaScript/TypeScript ecosystem:** Team familiarity with the ecosystem, large talent pool
- **Large community:** Extensive library support, active community for problem-solving
- **Faster development than native:** Enables meeting the aggressive 7-day MVP timeline
- **Hot reloading:** Speeds up development iteration cycles

## Alternatives Considered

### Flutter
- **Pros:** Good performance, beautiful UI, growing community
- **Cons:** Dart language less familiar, prefer JavaScript ecosystem for consistency with backend (NestJS)

### Native (Swift/Kotlin)
- **Pros:** Best performance, full platform access
- **Cons:** Requires maintaining two separate codebases, doubles development effort, too much duplication for our timeline

## Consequences

### Positive
- Single codebase reduces maintenance overhead
- Faster time-to-market
- Team can share skills between frontend and backend (TypeScript)
- Large ecosystem of packages for calendars, notifications, etc.

### Negative
- Some platform-specific features may require native modules
- Performance is slightly lower than fully native apps
- Dependency on React Native framework updates

### Mitigations
- Use Expo for simplified development where possible
- Profile performance early and optimize critical paths
- Plan for native module development if needed for widgets
