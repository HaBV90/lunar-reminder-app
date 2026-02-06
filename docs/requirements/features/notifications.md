# Notification System - He thong Thong bao

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Section:** 7.1.5 - Core Features

---

## FR-NOTIF-001: Kich ban thong bao mac dinh

**Priority:** Critical

### Cho Gio/Chap (nhac truoc 3 ngay):
- **3 ngay truoc:** 2 lan/ngay (6h30, 18h30)
- **2 ngay truoc:** 4 lan/ngay (6h30, 11h45, 18h30, 20h30)
- **1 ngay truoc:** 4 lan/ngay (6h30, 11h45, 18h30, 20h30)
- **Ngay dien ra:** 4 lan/ngay (6h30, 11h45, 18h30, 20h30)

### Cho Sinh nhat/Le/Ngay cuoi (nhac truoc 1 ngay):
- **1 ngay truoc:** 2 lan/ngay (6h30, 18h30)
- **Ngay dien ra:** 4 lan/ngay (6h30, 11h45, 18h30, 20h30)

---

## FR-NOTIF-002: Tuy chinh gio thong bao

**Priority:** Medium

### Details:
- User co the thay doi 4 khung gio mac dinh
- Setting global cho tat ca su kien
- Co the dieu chinh tung slot:
  - Slot 1: Sang (mac dinh 6h30)
  - Slot 2: Trua (mac dinh 11h45)
  - Slot 3: Toi (mac dinh 18h30)
  - Slot 4: Toi muon (mac dinh 20h30)

---

## FR-NOTIF-003: Tuy chinh so lan thong bao

**Priority:** Low

### Details:
- User co the giam so lan thong bao
- Vi du: Chi 2 lan/ngay thay vi 4 lan
- Chon cac slot nao muon nhan

---

## FR-NOTIF-004: Noi dung thong bao

**Priority:** High

### Format:
- Title: "Con X ngay den [Ten su kien]"
- Body: "[Ngay duong lich] ([Ngay am lich])"
- Action: Tap -> Mo app -> Xem chi tiet su kien

### Example:
- Title: "Con 3 ngay den Gio ong noi"
- Body: "18/05/2026 (20/4 am lich)"

---

## FR-NOTIF-005: Xu ly thong bao

**Priority:** High

### Details:
- **Nut "Dong"**: Tat lan nay, van nhan lan sau
- **Nut "Tat het"**: Khong nhan nua cho su kien nay
- **Nut "Nhac lai sau"**: Snooze 1 gio
- Trong app: Toggle on/off thong bao cho tung su kien

---

## FR-NOTIF-006: Notification Platform

**Priority:** Critical

### Platform: OneSignal

### Details:
- Push notification den iOS va Android
- Scheduled notifications (len lich truoc)
- Track delivery rate

---

## Notification Schedule Summary

### Gio/Chap Events (3-day advance)
| Day | Time Slots | Total Notifications |
|-----|------------|---------------------|
| D-3 | 6:30, 18:30 | 2 |
| D-2 | 6:30, 11:45, 18:30, 20:30 | 4 |
| D-1 | 6:30, 11:45, 18:30, 20:30 | 4 |
| D-0 | 6:30, 11:45, 18:30, 20:30 | 4 |
| **Total** | | **14** |

### Other Events (1-day advance)
| Day | Time Slots | Total Notifications |
|-----|------------|---------------------|
| D-1 | 6:30, 18:30 | 2 |
| D-0 | 6:30, 11:45, 18:30, 20:30 | 4 |
| **Total** | | **6** |

---

## Related Documentation

- [Event Management](./events.md)
- [Settings](./settings.md)
- [Use Cases](../use-cases.md)
- [User Stories](../user-stories.md)
- [Features Index](../index.md)
