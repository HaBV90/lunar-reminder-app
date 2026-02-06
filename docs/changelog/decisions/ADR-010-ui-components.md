# ADR-010: UI Component Strategy

**Date:** 2026-02-02
**Status:** Approved

## Context

The app needs a consistent set of UI components for buttons, inputs, cards, modals, and other interface elements. We needed to decide between using an existing component library or building custom components.

## Decision

We chose to **build custom UI components** rather than use a pre-built UI library.

## Rationale

- **Components relatively simple:** Our UI needs are straightforward (buttons, inputs, cards, lists)
- **Easier to maintain:** No dependency on external library updates or breaking changes
- **Full control over design:** Can implement the exact Vietnamese-themed design system
- **No dependency on library updates:** Avoid being blocked by library bugs or delays
- **Lighter bundle size:** Only include what we actually need

## Alternatives Considered

### React Native Paper
- **Pros:** Material Design, well-maintained, comprehensive
- **Cons:** Opinionated styling that would require significant overrides for Vietnamese theme

### NativeBase
- **Pros:** Extensive component library, themeable
- **Cons:** Heavy dependency, complex theming system, larger bundle size

### React Native Elements
- **Pros:** Popular, actively maintained
- **Cons:** Still requires customization, another dependency to manage

### Tamagui
- **Pros:** Performance-focused, cross-platform with web
- **Cons:** Newer library, learning curve, may be overkill

## Consequences

### Positive
- Exact match to UI/UX specifications
- Vietnamese patterns and colors implemented directly
- No third-party dependency conflicts
- Easier to debug and modify
- Team builds deep understanding of components

### Negative
- More initial development effort
- Need to handle edge cases ourselves
- Accessibility features must be implemented manually
- No pre-built complex components (date pickers, etc.)

### Mitigations
- Create a component library early in development
- Document component API and usage
- Use React Native's Accessibility API
- Borrow patterns from established libraries
- Consider external components for complex needs (calendar)

### Component Library Structure
```
src/components/
├── atoms/           # Basic elements
│   ├── Button/
│   ├── Text/
│   ├── Input/
│   └── Icon/
├── molecules/       # Combined elements
│   ├── EventCard/
│   ├── DatePicker/
│   └── NotificationBadge/
└── organisms/       # Complex components
    ├── EventList/
    ├── CalendarView/
    └── FamilyGroupCard/
```
