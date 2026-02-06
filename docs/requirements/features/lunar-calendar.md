# Lunar Calendar Conversion - Chuyen doi Lich Am

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Section:** 7.1.4 - Core Features

---

## FR-LUNAR-001: Chuyen doi am -> duong

**Priority:** Critical

### Details:
- Su dung thuat toan lich am Viet Nam (khac Trung Quoc)
- Tinh toan chinh xac theo mui gio Viet Nam (GMT+7)
- Xu ly thang nhuan:
  - Phat hien nam nhuan
  - Cho phep user chon lan 1, 2, hoac ca 2
  - Thong bao ro: "Thang 4 nhuan - Lan 1" hoac "Lan 2"

---

## FR-LUNAR-002: Hien thi song song

**Priority:** Medium

### Details:
- Moi noi hien ngay -> Hien thi ca duong va am
- Format: "18/05/2026 (20/4 am lich)"
- Calendar view: Ngay am hien duoi ngay duong

---

## FR-LUNAR-003: Validation ngay am lich

**Priority:** High

### Details:
- Thang: 1-12 (co the nhuan)
- Ngay: 1-30 (thang thieu co the 29 ngay)
- Validate theo thuat toan lich am

---

## Leap Month Handling

### When a leap month occurs:
1. Detect the leap month in the year
2. Show user options:
   - "Lan 1" (First occurrence)
   - "Lan 2" (Second occurrence, in leap month)
   - "Ca 2 lan" (Both occurrences)
3. If "Ca 2 lan" selected, create 2 separate events

### Display format:
- Normal month: "15/3 am lich"
- Leap month (lan 1): "15/3 am lich"
- Leap month (lan 2): "15/3 nhuan am lich"

---

## Lunar Calendar Algorithm Notes

### Vietnamese vs Chinese Lunar Calendar
- Vietnamese lunar calendar uses GMT+7 timezone
- Chinese lunar calendar uses GMT+8 timezone
- This can cause 1-day difference in some cases
- Our app MUST use Vietnamese calculations

### Key Differences
| Aspect | Vietnamese | Chinese |
|--------|-----------|---------|
| Timezone | GMT+7 | GMT+8 |
| New Year | Same day or 1 day diff | Reference |
| Leap month | Can differ | Reference |

---

## Related Documentation

- [Event Management](./events.md)
- [Calendar View](./calendar-view.md)
- [Notification System](./notifications.md)
- [Features Index](../index.md)
