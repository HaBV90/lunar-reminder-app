# ADR-012: Lunar Calendar Algorithm

**Date:** 2026-02-02
**Status:** Pending

## Context

The core feature of the app is converting between solar (Gregorian) and lunar (Vietnamese) calendar dates. This is critical for:
- Displaying lunar dates alongside solar dates
- Calculating recurring lunar events
- Handling leap months correctly
- Supporting events far in the past (death anniversaries)

## Decision

**To be decided** - Research and evaluation required before implementing event creation.

## Options Under Consideration

### Option 1: lunar-calendar-vn (npm package)
- **Pros:**
  - Vietnamese-specific implementation
  - Handles Can Chi (heavenly stems and earthly branches)
  - Active on npm
- **Cons:**
  - Need to verify accuracy
  - Unknown maintenance status
  - May not cover historical dates
- **Accuracy:** Needs verification
- **Effort:** Low

### Option 2: amlich (npm package)
- **Pros:**
  - Vietnamese lunar calendar focus
  - Includes traditional Vietnamese calendar terms
- **Cons:**
  - Verify date range support
  - Check leap month handling
- **Accuracy:** Needs verification
- **Effort:** Low

### Option 3: Custom Port from Reference Implementation
- **Pros:**
  - Full understanding of algorithm
  - Can optimize for our use case
  - Historical date support controllable
- **Cons:**
  - Significant development effort
  - Risk of bugs in implementation
  - Need to find reliable reference
- **Sources:**
  - Ho Ngoc Duc's algorithm (Vietnamese astronomer)
  - US Naval Observatory calculations
- **Effort:** High

### Option 4: lunar-javascript
- **Pros:**
  - Comprehensive lunar calendar library
  - Well-documented
- **Cons:**
  - Chinese-focused, may need adaptation for Vietnamese
  - Larger library
- **Effort:** Medium

## Evaluation Criteria

1. **Accuracy:** Correct lunar-solar conversion for dates 1900-2100
2. **Leap month handling:** Correct identification of leap months
3. **Vietnamese terms:** Support for Can Chi, Tiet Khi
4. **Historical dates:** Support for dates back to at least 1900
5. **Performance:** Fast calculations for calendar views
6. **Bundle size:** Reasonable addition to app
7. **Maintainability:** Clear code, good documentation

## Verification Process

1. Compare algorithm output against official Vietnamese calendar
2. Test leap month calculations for known years
3. Verify historical dates against records
4. Test edge cases (year boundaries, leap months)

## Consequences

### If using existing library
- Faster implementation
- Dependent on library accuracy
- May need patches for edge cases

### If building custom
- Full control over accuracy
- Significant development time
- Need expertise in astronomical calculations

## Priority

**HIGH** - This is a critical feature. Incorrect lunar dates would make the app unusable.

## Next Steps

1. Evaluate lunar-calendar-vn with test cases
2. Verify accuracy against official Vietnamese calendar 2026
3. Test leap month calculations
4. Create abstraction layer for algorithm switching
5. Make final decision before event creation implementation

## Deadline

Decision must be made before implementing event creation (estimated Day 3 of development).

## References

- Ho Ngoc Duc's Vietnamese Calendar: http://www.informatik.uni-leipzig.de/~duc/amlich/
- Vietnamese National Observatory publications
- Traditional Vietnamese calendar rules
