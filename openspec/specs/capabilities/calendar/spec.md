# Calendar Capability Specification

## Overview

Lunar calendar conversion and calendar view capability for the Lunar Reminder App.

---

## Features

### FR-LUNAR-001: Lunar to Solar Conversion
- **Priority:** Critical
- Vietnamese lunar calendar algorithm (GMT+7)
- Different from Chinese calendar by timezone
- Handle leap months:
  - Detect leap year
  - User choice: 1st, 2nd, or both occurrences
  - Clear labeling: "Tháng 4 nhuận - Lần 1/2"

### FR-LUNAR-002: Dual Date Display
- **Priority:** Medium
- Show both solar and lunar everywhere
- Format: "18/05/2026 (20/4 âm lịch)"
- Calendar view: lunar date below solar date

### FR-LUNAR-003: Lunar Date Validation
- Month: 1-12 (may be leap)
- Day: 1-30 (some months 29 days)
- Validate against lunar calendar rules

### FR-CALVIEW-001: Month Calendar View
- **Priority:** Low (optional for MVP)
- Solar calendar grid layout
- Lunar dates shown small below
- Event markers on dates
- Tap date → show day's events

### FR-CALVIEW-002: Navigation
- Swipe left/right for months
- "Today" button
- Month/year header

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/lunar/convert` | Convert lunar to solar |
| POST | `/lunar/year-info` | Get year information |

---

## Conversion Algorithm

### Vietnamese Lunar Calendar
- Timezone: GMT+7 (not GMT+8 like Chinese)
- New moon calculation at 0:00 Hanoi time
- May differ 1 day from Chinese calendar

### Implementation Options
1. **Library:** `lunar-calendar-vn` (npm)
2. **Library:** `amlich` (GitHub: vanng822/amlich)
3. **Custom:** Port from reference implementation

### Key Types

```typescript
interface LunarDate {
  day: number;      // 1-30
  month: number;    // 1-12
  year?: number;    // Optional for recurring
  isLeap: boolean;
}

interface YearInfo {
  year: number;
  isLeapYear: boolean;
  leapMonth: number | null;
  lunarNewYear: Date;
  totalDays: number;
}
```

---

## Calendar Export

### FR-CAL-001: Google Calendar Export
- Export all events for next year
- Convert lunar → solar dates
- Set yearly repeat
- Include reminders

### FR-CAL-002: Device Calendar Export
- iOS: Apple Calendar
- Android: Google Calendar / default

### FR-CAL-003: Year-End Reminder
- December, 3 days before new year
- Notification: "Sắp sang năm mới! Export lịch?"
- Quick action to export

---

## User Stories

**US-CAL-1:** As a user, I want lunar dates converted automatically so I don't have to calculate.

**US-CAL-2:** As a user, I want to see a calendar view so I can plan around events.

**US-CAL-3:** As a user, I want to export to Google Calendar so events sync with my work calendar.

---

## Reference Documentation

- [Architecture Overview](../../../docs/architecture/index.md)
- [Data Types & Enums](../../../docs/data-model/enums.md) — CalendarType, LunarLeapOption
- [Glossary](../../../docs/reference/glossary.md) — Lunar calendar terms
- [Frontend Setup Guide](../../../docs/guides/FRONTEND_SETUP.md)

---

## Related Specs

- [Events Capability](../events/spec.md)
- [Notifications Capability](../notifications/spec.md)
