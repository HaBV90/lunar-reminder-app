# Lunar Calendar Skill

This skill provides Vietnamese lunar calendar conversion algorithms and patterns.

## Vietnamese Lunar Calendar Overview

The Vietnamese lunar calendar differs from the Chinese lunar calendar due to timezone differences:
- Vietnam: GMT+7 (Asia/Ho_Chi_Minh)
- China: GMT+8 (Asia/Shanghai)

This can cause the new moon (and thus the start of lunar months) to fall on different days.

## Core Interfaces

```typescript
// types/lunar-calendar.ts

export interface LunarDate {
  day: number;      // 1-30
  month: number;    // 1-12
  year: number;     // e.g., 2026
  isLeap: boolean;  // Whether this is a leap month
}

export interface SolarDate {
  day: number;
  month: number;
  year: number;
}

export interface LunarYearInfo {
  year: number;
  isLeapYear: boolean;
  leapMonth: number | null;    // Which month is leap (1-12), null if not leap year
  lunarNewYear: Date;          // Solar date of Tết
  totalDays: number;
  months: LunarMonthInfo[];
}

export interface LunarMonthInfo {
  month: number;       // 1-12
  isLeap: boolean;
  startDate: Date;     // Solar start date
  endDate: Date;       // Solar end date
  days: number;        // 29 or 30
}

export interface ConversionResult {
  solarDate: Date;
  lunarDate: LunarDate;
  yearInfo: LunarYearInfo;
}
```

## Vietnamese Lunar Calendar Service

```typescript
// modules/lunar-calendar/lunar-calendar.service.ts
import { Injectable } from '@nestjs/common';
import { LunarDate, SolarDate, LunarYearInfo, LunarMonthInfo } from './types';

// Vietnam timezone offset in hours
const VIETNAM_TIMEZONE = 7;

// Julian day constants
const JULIAN_DAY_OFFSET = 2415021; // January 1, 1900

@Injectable()
export class LunarCalendarService {

  /**
   * Convert lunar date to solar date
   */
  convertToSolar(lunarDate: LunarDate): Date {
    const yearInfo = this.getYearInfo(lunarDate.year);

    // Find the target lunar month
    const targetMonth = yearInfo.months.find(
      m => m.month === lunarDate.month && m.isLeap === lunarDate.isLeap
    );

    if (!targetMonth) {
      throw new Error(`Invalid lunar date: month ${lunarDate.month} not found in year ${lunarDate.year}`);
    }

    if (lunarDate.day > targetMonth.days) {
      throw new Error(`Invalid lunar day: ${lunarDate.day} exceeds month days (${targetMonth.days})`);
    }

    // Calculate solar date
    const solarDate = new Date(targetMonth.startDate);
    solarDate.setDate(solarDate.getDate() + lunarDate.day - 1);

    return solarDate;
  }

  /**
   * Convert solar date to lunar date
   */
  convertToLunar(solarDate: Date): LunarDate {
    const year = solarDate.getFullYear();

    // Check current year first, then previous/next if needed
    for (const checkYear of [year, year - 1, year + 1]) {
      const yearInfo = this.getYearInfo(checkYear);

      for (const month of yearInfo.months) {
        if (solarDate >= month.startDate && solarDate < month.endDate) {
          const day = Math.floor(
            (solarDate.getTime() - month.startDate.getTime()) / (24 * 60 * 60 * 1000)
          ) + 1;

          return {
            day,
            month: month.month,
            year: checkYear,
            isLeap: month.isLeap,
          };
        }
      }
    }

    throw new Error(`Could not convert solar date: ${solarDate.toISOString()}`);
  }

  /**
   * Get detailed information about a lunar year
   */
  getYearInfo(year: number): LunarYearInfo {
    // Calculate new moons for the year
    const newMoons = this.calculateNewMoons(year);

    // Identify winter solstice dates
    const winterSolstice11 = this.getWinterSolstice(year - 1);
    const winterSolstice12 = this.getWinterSolstice(year);

    // Find leap month
    const { months, leapMonth, isLeapYear } = this.identifyLunarMonths(
      newMoons,
      winterSolstice11,
      winterSolstice12
    );

    // Find lunar new year (month 1, day 1)
    const month1 = months.find(m => m.month === 1 && !m.isLeap);
    const lunarNewYear = month1?.startDate || new Date();

    const totalDays = months.reduce((sum, m) => sum + m.days, 0);

    return {
      year,
      isLeapYear,
      leapMonth,
      lunarNewYear,
      totalDays,
      months,
    };
  }

  /**
   * Calculate new moon dates using astronomical algorithm
   * Based on Meeus algorithm adapted for Vietnam timezone
   */
  private calculateNewMoons(year: number): Date[] {
    const newMoons: Date[] = [];

    // Calculate approximately 15 new moons (covering year and buffer)
    const startK = this.getNewMoonK(year - 1, 11); // Start from Nov of previous year

    for (let i = 0; i < 15; i++) {
      const k = startK + i;
      const jd = this.newMoonJD(k);
      const date = this.jdToDate(jd);
      newMoons.push(date);
    }

    return newMoons;
  }

  /**
   * Get the k value for new moon calculation
   * k = (year - 2000) * 12.3685
   */
  private getNewMoonK(year: number, month: number): number {
    return Math.floor((year + (month - 1) / 12 - 2000) * 12.3685);
  }

  /**
   * Calculate Julian Day of new moon for k-th lunation
   * Using Meeus algorithm
   */
  private newMoonJD(k: number): number {
    const T = k / 1236.85;
    const T2 = T * T;
    const T3 = T2 * T;
    const T4 = T3 * T;

    // Mean phase
    let JDE = 2451550.09766 + 29.530588861 * k
              + 0.00015437 * T2
              - 0.000000150 * T3
              + 0.00000000073 * T4;

    // Sun's mean anomaly
    const M = (2.5534 + 29.10535670 * k
              - 0.0000014 * T2
              - 0.00000011 * T3) % 360;

    // Moon's mean anomaly
    const Mp = (201.5643 + 385.81693528 * k
               + 0.0107582 * T2
               + 0.00001238 * T3
               - 0.000000058 * T4) % 360;

    // Moon's argument of latitude
    const F = (160.7108 + 390.67050284 * k
              - 0.0016118 * T2
              - 0.00000227 * T3
              + 0.000000011 * T4) % 360;

    // Longitude of ascending node
    const Omega = (124.7746 - 1.56375588 * k
                   + 0.0020672 * T2
                   + 0.00000215 * T3) % 360;

    // Corrections
    const toRad = Math.PI / 180;

    const correction =
      - 0.40720 * Math.sin(Mp * toRad)
      + 0.17241 * Math.sin(M * toRad)
      + 0.01608 * Math.sin(2 * Mp * toRad)
      + 0.01039 * Math.sin(2 * F * toRad)
      + 0.00739 * Math.sin((Mp - M) * toRad)
      - 0.00514 * Math.sin((Mp + M) * toRad)
      + 0.00208 * Math.sin(2 * M * toRad)
      - 0.00111 * Math.sin((Mp - 2 * F) * toRad)
      - 0.00057 * Math.sin((Mp + 2 * F) * toRad)
      + 0.00056 * Math.sin((2 * Mp + M) * toRad)
      - 0.00042 * Math.sin(3 * Mp * toRad)
      + 0.00042 * Math.sin((M + 2 * F) * toRad)
      + 0.00038 * Math.sin((M - 2 * F) * toRad)
      - 0.00024 * Math.sin((2 * Mp - M) * toRad)
      - 0.00007 * Math.sin((Mp + 2 * M) * toRad)
      - 0.00017 * Math.sin(Omega * toRad);

    JDE += correction;

    // Apply Vietnam timezone offset
    JDE += VIETNAM_TIMEZONE / 24;

    return JDE;
  }

  /**
   * Get winter solstice date for a given year
   */
  private getWinterSolstice(year: number): Date {
    // Approximate winter solstice (December 21-22)
    // Using more precise calculation
    const JD2000 = 2451545.0;
    const y = (year - 2000) / 1000;

    let jd = 2451900.05952 + 365242.74049 * y
             - 0.06223 * y * y
             - 0.00823 * y * y * y;

    // Adjust for Vietnam timezone
    jd += VIETNAM_TIMEZONE / 24;

    return this.jdToDate(jd);
  }

  /**
   * Identify lunar months and find leap month
   */
  private identifyLunarMonths(
    newMoons: Date[],
    winterSolstice11: Date,
    winterSolstice12: Date
  ): { months: LunarMonthInfo[]; leapMonth: number | null; isLeapYear: boolean } {
    const months: LunarMonthInfo[] = [];

    // Find month containing winter solstice 11
    let month11Index = -1;
    for (let i = 0; i < newMoons.length - 1; i++) {
      if (winterSolstice11 >= newMoons[i] && winterSolstice11 < newMoons[i + 1]) {
        month11Index = i;
        break;
      }
    }

    // Find month containing winter solstice 12
    let month12Index = -1;
    for (let i = month11Index + 1; i < newMoons.length - 1; i++) {
      if (winterSolstice12 >= newMoons[i] && winterSolstice12 < newMoons[i + 1]) {
        month12Index = i;
        break;
      }
    }

    // Check if leap year (13 months between winter solstices)
    const monthCount = month12Index - month11Index;
    const isLeapYear = monthCount === 13;
    let leapMonth: number | null = null;

    // Assign month numbers
    let currentMonth = 11;
    let foundLeap = false;

    for (let i = month11Index; i < month12Index; i++) {
      const startDate = new Date(newMoons[i]);
      const endDate = new Date(newMoons[i + 1]);
      const days = Math.round((endDate.getTime() - startDate.getTime()) / (24 * 60 * 60 * 1000));

      let isLeap = false;

      if (isLeapYear && !foundLeap && i > month11Index) {
        // Check if this month has no major solar term
        const hasMajorSolarTerm = this.hasMajorSolarTerm(startDate, endDate);
        if (!hasMajorSolarTerm) {
          isLeap = true;
          foundLeap = true;
          leapMonth = currentMonth;
        }
      }

      months.push({
        month: currentMonth,
        isLeap,
        startDate,
        endDate,
        days,
      });

      if (!isLeap) {
        currentMonth++;
        if (currentMonth > 12) currentMonth = 1;
      }
    }

    return { months, leapMonth, isLeapYear };
  }

  /**
   * Check if a lunar month contains a major solar term (中气)
   */
  private hasMajorSolarTerm(startDate: Date, endDate: Date): boolean {
    // Major solar terms occur approximately every 30.44 days
    // Starting from Winter Solstice
    const year = startDate.getFullYear();
    const majorTerms = this.getMajorSolarTerms(year);

    return majorTerms.some(term => term >= startDate && term < endDate);
  }

  /**
   * Get major solar term dates for a year
   */
  private getMajorSolarTerms(year: number): Date[] {
    const terms: Date[] = [];
    const JD2000 = 2451545.0;

    // 12 major solar terms (中气)
    // Start from Winter Solstice and go every 30 degrees
    for (let i = 0; i < 12; i++) {
      const angle = i * 30; // 0, 30, 60, ..., 330
      const jd = this.solarTermJD(year, angle);
      terms.push(this.jdToDate(jd));
    }

    return terms;
  }

  /**
   * Calculate Julian Day of a solar term
   */
  private solarTermJD(year: number, angle: number): number {
    // Simplified calculation - more precise algorithm available
    const y = year - 2000;
    const baseJD = 2451545.0 + 365.2422 * y;

    // Approximate based on angle from winter solstice
    const dayOffset = (angle / 360) * 365.2422;

    // Winter solstice is around Dec 21
    const winterSolsticeOffset = 355; // Days from Jan 1 to Dec 21

    return baseJD + winterSolsticeOffset + dayOffset - 365;
  }

  /**
   * Convert Julian Day to Date
   */
  private jdToDate(jd: number): Date {
    const Z = Math.floor(jd + 0.5);
    const F = jd + 0.5 - Z;

    let A: number;
    if (Z < 2299161) {
      A = Z;
    } else {
      const alpha = Math.floor((Z - 1867216.25) / 36524.25);
      A = Z + 1 + alpha - Math.floor(alpha / 4);
    }

    const B = A + 1524;
    const C = Math.floor((B - 122.1) / 365.25);
    const D = Math.floor(365.25 * C);
    const E = Math.floor((B - D) / 30.6001);

    const day = B - D - Math.floor(30.6001 * E);
    const month = E < 14 ? E - 1 : E - 13;
    const year = month > 2 ? C - 4716 : C - 4715;

    const hours = F * 24;
    const hour = Math.floor(hours);
    const minutes = (hours - hour) * 60;
    const minute = Math.floor(minutes);

    return new Date(year, month - 1, day, hour, minute);
  }

  /**
   * Format lunar date for display
   */
  formatLunarDate(lunarDate: LunarDate, locale: string = 'vi'): string {
    const leapText = lunarDate.isLeap
      ? (locale === 'vi' ? ' nhuận' : ' (leap)')
      : '';

    if (locale === 'vi') {
      return `${lunarDate.day}/${lunarDate.month}${leapText} âm lịch`;
    }
    return `Lunar ${lunarDate.month}${leapText}/${lunarDate.day}`;
  }

  /**
   * Get Vietnamese zodiac year name
   */
  getZodiacYear(year: number): { can: string; chi: string; fullName: string } {
    const can = ['Giáp', 'Ất', 'Bính', 'Đinh', 'Mậu', 'Kỷ', 'Canh', 'Tân', 'Nhâm', 'Quý'];
    const chi = ['Tý', 'Sửu', 'Dần', 'Mão', 'Thìn', 'Tỵ', 'Ngọ', 'Mùi', 'Thân', 'Dậu', 'Tuất', 'Hợi'];

    const canIndex = (year - 4) % 10;
    const chiIndex = (year - 4) % 12;

    return {
      can: can[canIndex],
      chi: chi[chiIndex],
      fullName: `${can[canIndex]} ${chi[chiIndex]}`,
    };
  }

  /**
   * Validate a lunar date
   */
  validateLunarDate(lunarDate: LunarDate): { valid: boolean; error?: string } {
    if (lunarDate.day < 1 || lunarDate.day > 30) {
      return { valid: false, error: 'Day must be between 1 and 30' };
    }

    if (lunarDate.month < 1 || lunarDate.month > 12) {
      return { valid: false, error: 'Month must be between 1 and 12' };
    }

    if (lunarDate.year < 1900 || lunarDate.year > 2100) {
      return { valid: false, error: 'Year must be between 1900 and 2100' };
    }

    // Check if leap month is valid for this year
    if (lunarDate.isLeap) {
      const yearInfo = this.getYearInfo(lunarDate.year);
      if (!yearInfo.isLeapYear || yearInfo.leapMonth !== lunarDate.month) {
        return {
          valid: false,
          error: `Month ${lunarDate.month} is not a leap month in year ${lunarDate.year}`
        };
      }
    }

    // Check if day is valid for this month
    try {
      const yearInfo = this.getYearInfo(lunarDate.year);
      const month = yearInfo.months.find(
        m => m.month === lunarDate.month && m.isLeap === lunarDate.isLeap
      );

      if (month && lunarDate.day > month.days) {
        return {
          valid: false,
          error: `Month ${lunarDate.month} only has ${month.days} days`
        };
      }
    } catch {
      // If year info calculation fails, allow the date
    }

    return { valid: true };
  }
}
```

## Usage Examples

### Converting Lunar to Solar

```typescript
const lunarService = new LunarCalendarService();

// Convert lunar date 15/3 (March 15 lunar) to solar
const solarDate = lunarService.convertToSolar({
  day: 15,
  month: 3,
  year: 2026,
  isLeap: false,
});

console.log(solarDate); // 2026-04-18
```

### Converting Solar to Lunar

```typescript
const lunarDate = lunarService.convertToLunar(new Date('2026-04-18'));

console.log(lunarDate);
// { day: 15, month: 3, year: 2026, isLeap: false }
```

### Handling Leap Months

```typescript
// Check if year has a leap month
const yearInfo = lunarService.getYearInfo(2026);

if (yearInfo.isLeapYear) {
  console.log(`Leap month: ${yearInfo.leapMonth}`);

  // Create event for leap month
  const leapEvent = {
    lunarDay: 10,
    lunarMonth: yearInfo.leapMonth,
    isLeap: true,
    leapOption: 'first', // 'first', 'second', or 'both'
  };
}
```

### Leap Month Options

When an event falls in a potential leap month:

1. **first** - Only notify in the first occurrence (regular month)
2. **second** - Only notify in the second occurrence (leap month)
3. **both** - Notify in both occurrences

```typescript
// User selects how to handle leap month
interface EventLeapOption {
  lunarMonth: number;
  leapOption: 'first' | 'second' | 'both';
}

// Calculate notification dates
function getEventDates(event: Event, year: number): Date[] {
  const yearInfo = lunarService.getYearInfo(year);
  const dates: Date[] = [];

  // Regular month occurrence
  if (event.leapOption !== 'second') {
    dates.push(lunarService.convertToSolar({
      day: event.lunarDay,
      month: event.lunarMonth,
      year,
      isLeap: false,
    }));
  }

  // Leap month occurrence (if applicable)
  if (yearInfo.leapMonth === event.lunarMonth && event.leapOption !== 'first') {
    dates.push(lunarService.convertToSolar({
      day: event.lunarDay,
      month: event.lunarMonth,
      year,
      isLeap: true,
    }));
  }

  return dates;
}
```

## Frontend Integration

```typescript
// hooks/useLunarCalendar.ts
import { useMemo } from 'react';
import { LunarCalendarService } from '@utils/lunarCalendar';

export const useLunarCalendar = () => {
  const service = useMemo(() => new LunarCalendarService(), []);

  const convertToSolar = (lunarDate: LunarDate): Date => {
    return service.convertToSolar(lunarDate);
  };

  const convertToLunar = (solarDate: Date): LunarDate => {
    return service.convertToLunar(solarDate);
  };

  const formatLunarDate = (lunarDate: LunarDate): string => {
    return service.formatLunarDate(lunarDate);
  };

  const getYearInfo = (year: number) => {
    return service.getYearInfo(year);
  };

  const getZodiacYear = (year: number) => {
    return service.getZodiacYear(year);
  };

  return {
    convertToSolar,
    convertToLunar,
    formatLunarDate,
    getYearInfo,
    getZodiacYear,
  };
};
```
