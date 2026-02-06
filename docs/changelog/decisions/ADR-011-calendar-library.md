# ADR-011: Calendar Library

**Date:** 2026-02-02
**Status:** Pending

## Context

The app needs a calendar view to display lunar events. This includes monthly and yearly views with lunar date overlay, event indicators, and interactive date selection. We need to evaluate whether to use an existing library or build a custom solution.

## Decision

**To be decided** - Research and evaluation required before implementing the Calendar View Screen.

## Options Under Consideration

### Option 1: react-native-calendars
- **Pros:**
  - Most popular RN calendar library
  - Customizable themes
  - Agenda and multi-dot support
  - Active maintenance
- **Cons:**
  - May need significant customization for lunar dates
  - Performance with many events unknown
- **Effort:** Medium

### Option 2: react-native-calendar-picker
- **Pros:**
  - Simple API
  - Lightweight
  - Easy to customize
- **Cons:**
  - Less feature-rich
  - May not support complex requirements
- **Effort:** Low

### Option 3: Custom Implementation
- **Pros:**
  - Full control over lunar date display
  - Exact match to design specifications
  - Optimized for our specific use case
- **Cons:**
  - Significant development effort
  - Need to handle all edge cases
  - More maintenance burden
- **Effort:** High

### Option 4: @marceloterreiro/flash-calendar
- **Pros:**
  - Built on FlashList for performance
  - Modern architecture
- **Cons:**
  - Newer library, less proven
- **Effort:** Medium

## Evaluation Criteria

1. **Lunar date overlay support:** Can display both solar and lunar dates
2. **Customization:** Theme to match Vietnamese design system
3. **Performance:** Handle years of events smoothly
4. **Event indicators:** Show multiple event types per day
5. **Accessibility:** Screen reader support
6. **Bundle size:** Reasonable addition to app size

## Consequences

### If using existing library
- Faster initial development
- May need workarounds for lunar dates
- Dependency on library updates

### If building custom
- Perfect fit for requirements
- More development time
- Full ownership and control

## Next Steps

1. Create proof-of-concept with react-native-calendars
2. Test lunar date overlay implementation
3. Evaluate performance with 1000+ events
4. Make final decision before Sprint 2

## Deadline

Decision must be made before implementing Calendar View Screen (estimated Day 3-4 of development).
