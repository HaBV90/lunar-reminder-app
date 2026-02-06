# Lunar Calendar Implementation

## Overview

The Vietnamese lunar calendar differs from the Chinese calendar primarily due to timezone (GMT+7 vs GMT+8). This can cause dates to differ by 1 day.

---

## Key Differences from Chinese Calendar

| Aspect | Vietnamese | Chinese |
|--------|------------|---------|
| Timezone | GMT+7 | GMT+8 |
| New moon calculation | 0:00 Hanoi time | 0:00 Beijing time |
| Reference | Vietnamese standards | Chinese standards |

---

## Implementation Approach

### Option 1: Use Existing Library (Recommended)

```bash
npm install lunar-calendar-vn
```

### Option 2: Libraries to Research
- `lunar-calendar-vn` (npm)
- `amlich` (GitHub: vanng822/amlich)
- Custom port from: https://www.informatik.uni-leipzig.de/~duc/amlich/

### Evaluation Criteria
- Accuracy (must match official Vietnamese calendar)
- Performance
- Maintenance status
- TypeScript support
- License

---

## Core Service

```typescript
// lunar-calendar.service.ts
@Injectable()
export class LunarCalendarService {
  private readonly TIMEZONE_OFFSET = 7; // GMT+7

  /**
   * Convert lunar date to solar date for a given year
   */
  convertToSolar(lunar: LunarDate, year: number): Date {
    // 1. Get new moon dates for the year
    const newMoons = this.calculateNewMoons(year);

    // 2. Identify lunar months
    const lunarMonths = this.identifyLunarMonths(newMoons, year);

    // 3. Find the target month
    let targetMonth = lunarMonths[lunar.month - 1];
    if (lunar.isLeap) {
      targetMonth = lunarMonths.find(m => m.month === lunar.month && m.isLeap);
    }

    // 4. Calculate solar date
    const solarDate = new Date(targetMonth.startDate);
    solarDate.setDate(solarDate.getDate() + lunar.day - 1);

    return solarDate;
  }

  /**
   * Convert solar date to lunar date
   */
  convertToLunar(solar: Date): LunarDate {
    // Implementation details...
  }

  /**
   * Get year information (leap year, leap month)
   */
  getYearInfo(year: number): YearInfo {
    // Implementation details...
  }
}
```

---

## TypeScript Types

```typescript
interface LunarDate {
  day: number;      // 1-30
  month: number;    // 1-12
  year?: number;    // Optional for recurring events
  isLeap: boolean;
}

interface YearInfo {
  year: number;
  isLeapYear: boolean;
  leapMonth: number | null;
  lunarNewYear: Date;
  totalDays: number;
}

interface LunarMonth {
  month: number;
  year: number;
  isLeap: boolean;
  startDate: Date;
  endDate: Date;
  days: number;
}
```

---

## Leap Month Handling

Vietnamese lunar calendar has leap months (tháng nhuận) approximately 7 times every 19 years.

### Detection
- A lunar month without a major solar term is a leap month
- Calculate 24 solar terms (tiết khí)
- Identify which month lacks a major term

### User Options
When event falls in a leap month year:
- `first`: Use first occurrence only
- `second`: Use second occurrence only
- `both`: Create notifications for both

---

## Algorithm Reference

The core algorithm involves:
1. Calculate new moon times using astronomical formulas
2. Adjust for GMT+7 timezone
3. Identify 24 solar terms
4. Determine leap month based on solar terms
5. Map lunar months to solar date ranges

Reference implementation: https://www.informatik.uni-leipzig.de/~duc/amlich/

---

## Related

- [Calendar Capability Spec](../../openspec/specs/capabilities/calendar/spec.md)
- [Events API](../api/events-endpoints.md)
