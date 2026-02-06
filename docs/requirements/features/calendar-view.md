# Calendar View - Xem Lich

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Section:** 7.1.8 - Core Features

---

## FR-CALVIEW-001: Lich thang (Optional)

**Priority:** Low

### Display:
- Lich duong lam chinh (grid view)
- Moi o ngay duong co ngay am ben duoi (nho hon)
- Su kien hien thi bang icon nho duoi ngay

### Interaction:
- Tap vao ngay -> Hien danh sach su kien cua ngay do (bottom sheet)
- Tap vao icon su kien -> Xem chi tiet su kien

---

## FR-CALVIEW-002: Dieu huong

**Priority:** Low

### Details:
- Swipe left/right de chuyen thang
- Button "Hom nay" de ve ngay hien tai
- Hien thi ten thang + nam

---

## Calendar View Mockup

### Month View
```
+----------------------------------+
| <  Thang 5, 2026           >     |
+----------------------------------+
| CN   T2   T3   T4   T5   T6   T7 |
+----------------------------------+
|                          1    2  |
|                         4AL  5AL |
+----------------------------------+
|  3    4    5    6    7    8    9 |
| 6AL  7AL  8AL  9AL 10AL 11AL 12AL|
+----------------------------------+
| 10   11   12   13   14   15   16 |
|13AL 14AL 15AL 16AL 17AL 18AL 19AL|
|                [*]               |
+----------------------------------+
| 17   18   19   20   21   22   23 |
|20AL 21AL 22AL 23AL 24AL 25AL 26AL|
|      [*]                         |
+----------------------------------+
| 24   25   26   27   28   29   30 |
|27AL 28AL 29AL 1/5AL 2AL  3AL  4AL|
+----------------------------------+
| 31                               |
| 5AL                              |
+----------------------------------+

[*] = Event indicator
```

### Day Detail (Bottom Sheet)
```
+----------------------------------+
| 18 Thang 5, 2026                 |
| (20/4 am lich)                   |
+----------------------------------+
| [Icon] Gio ba ngoai              |
|        Con 0 ngay                |
+----------------------------------+
| [Icon] Sinh nhat Anh Hung        |
|        Con 0 ngay                |
+----------------------------------+
```

---

## Navigation Controls

### Gestures
| Gesture | Action |
|---------|--------|
| Swipe Left | Next month |
| Swipe Right | Previous month |
| Tap date | Show day events |
| Tap event icon | Open event detail |

### Buttons
| Button | Action |
|--------|--------|
| "Hom nay" | Return to current date |
| "<" | Previous month |
| ">" | Next month |

---

## Related Documentation

- [Lunar Calendar Conversion](./lunar-calendar.md)
- [Event Management](./events.md)
- [Widget](./widget.md)
- [Features Index](../index.md)
