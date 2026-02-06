# Widget

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Section:** 7.1.10 - Core Features

---

## FR-WIDGET-001: Home screen widget

**Priority:** Medium

### Kich thuoc:
- **Nho (2x2):** 1 su kien gan nhat + countdown
- **Vua (4x2):** 2-3 su kien
- **Lon (4x4):** Mini calendar + 3-4 su kien

---

## FR-WIDGET-002: Noi dung hien thi

**Priority:** Medium

### Details:
- Ten su kien
- Icon loai su kien
- Countdown: "Con X ngay"
- Ngay duong lich + am lich

---

## FR-WIDGET-003: Tuong tac

**Priority:** Medium

### Details:
- Tap vao widget -> Mo app
- Neu 1 ngay co 1 su kien: Tap icon -> Xem chi tiet
- Neu nhieu su kien: Tap -> Mo app (danh sach ngay do)

---

## FR-WIDGET-004: Cap nhat

**Priority:** Medium

### Details:
- Auto refresh moi 1 gio
- Update ngay khi co thay doi trong app

---

## Widget Size Specifications

### Small Widget (2x2)
```
+------------------+
| [Icon] Gio ong   |
|                  |
| Con 3 ngay       |
| 18/5 (20/4 AL)   |
+------------------+
```

### Medium Widget (4x2)
```
+----------------------------------+
| Su kien sap toi                  |
+----------------------------------+
| [Icon] Gio ong noi    Con 3 ngay |
| [Icon] Sinh nhat me   Con 7 ngay |
| [Icon] Le Vu Lan     Con 15 ngay |
+----------------------------------+
```

### Large Widget (4x4)
```
+----------------------------------+
| Thang 5, 2026                    |
+----------------------------------+
| CN T2 T3 T4 T5 T6 T7             |
| .. .. .. .. .. .. 1              |
| 2  3  4  5  6  7  8              |
| 9  10 11 12 13 14 15             |
| 16 17 18*19 20 21 22             |
| 23 24 25 26 27 28 29             |
| 30 31 .. .. .. .. ..             |
+----------------------------------+
| Su kien sap toi:                 |
| [Icon] Gio ong noi (18/5)        |
| [Icon] Sinh nhat me (25/5)       |
+----------------------------------+
```

---

## Platform-Specific Notes

### iOS (WidgetKit)
- Supports Small, Medium, Large sizes
- Background refresh every 15-60 minutes
- Limited interactivity

### Android (App Widget)
- Flexible sizing
- More frequent updates possible
- Supports clicks and buttons

---

## Related Documentation

- [Event Management](./events.md)
- [Calendar View](./calendar-view.md)
- [Notification System](./notifications.md)
- [Features Index](../index.md)
