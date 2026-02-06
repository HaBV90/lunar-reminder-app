# Calendar Integration - Tich hop Lich

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Section:** 7.1.7 - Core Features

---

## FR-CAL-001: Export sang Google Calendar

**Priority:** Medium

### Details:
- Export tat ca su kien cua nam tiep theo
- Chuyen doi lich am -> duong
- Tao su kien voi reminder trong Google Calendar
- Su kien duoc danh dau "repeat yearly"

---

## FR-CAL-002: Export sang lich thiet bi

**Priority:** Medium

### Details:
- iOS: Export vao Apple Calendar
- Android: Export vao Google Calendar hoac calendar mac dinh
- Tuong tu FR-CAL-001

---

## FR-CAL-003: Nhac export cuoi nam

**Priority:** Medium

### Details:
- Vao thang 12, 3 ngay truoc nam moi
- Notification: "Sap sang nam moi! Export lich sang nam sau?"
- Tan suat: Tuong tu thong bao gio (2-4 lan/ngay)
- Co nut quick action de export ngay

---

## FR-CAL-004: Them vao app nhac nho

**Priority:** Low

### Details:
- Export sang Reminders (iOS) hoac Google Tasks (Android)
- Format tuong tu

---

## Export Process

### Step-by-step Export to Google Calendar
```
1. User taps "Export" button
2. App requests Google Calendar permission
3. User grants permission
4. App calculates solar dates for next year
5. For each event:
   a. Convert lunar date to solar date
   b. Create Google Calendar event
   c. Set reminder according to event type
   d. Mark as yearly recurring
6. Show success message with count
```

### Exported Event Format
```
Title: [Event Name] (Am lich: [Lunar Date])
Date: [Solar Date]
Reminder:
  - Gio events: 3 days before
  - Other events: 1 day before
Recurrence: Yearly
Notes: Exported from Lunar Reminder App
```

---

## Year-End Export Reminder Schedule

| Day | Notification |
|-----|-------------|
| Dec 29 | "3 ngay nua la nam moi! Export lich?" |
| Dec 30 | "2 ngay nua la nam moi! Export lich?" |
| Dec 31 | "Ngay mai la nam moi! Export lich ngay?" |

---

## Related Documentation

- [Event Management](./events.md)
- [Lunar Calendar Conversion](./lunar-calendar.md)
- [Notification System](./notifications.md)
- [Calendar View](./calendar-view.md)
- [Features Index](../index.md)
