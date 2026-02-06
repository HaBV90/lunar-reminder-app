# Event Management - Quan ly Su kien

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Section:** 7.1.2 - Core Features

---

## FR-EVENT-001: Tao su kien moi

**Priority:** Critical

### Fields bat buoc:
- Ten su kien (text, max 100 chars)
- Loai su kien (dropdown): Gio/Ky, Sinh nhat, Le hoi, Ngay cuoi, Ky niem, Khac
- Loai lich (radio): Am lich / Duong lich
- Ngay thang (date picker theo loai lich)

### Fields optional:
- Nhom su kien (dropdown, co the tao moi)
- Ghi chu (textarea, max 500 chars)
- Nhac truoc (dropdown): Theo mac dinh / Tuy chinh

### Business Rules:
- Neu chon "Gio/Ky" -> Mac dinh lich am, nhac truoc 3 ngay
- Neu chon "Sinh nhat" -> Mac dinh lich duong, nhac truoc 1 ngay
- Neu ngay am lich roi vao thang nhuan -> Hien option chon lan 1, lan 2, hoac ca 2

### Validation:
- Ten khong duoc de trong
- Ngay phai hop le (khong duoc qua khu cho su kien moi)
- Neu chon "Ca 2 lan thang nhuan" -> Tao 2 su kien rieng trong nam nhuan

---

## FR-EVENT-002: Xem danh sach su kien

**Priority:** Critical

### Display:
- Sap xep theo thoi gian (sap toi truoc)
- Hien thi: Ten, Ngay duong lich, Ngay am lich, Con X ngay
- Icon phan biet loai su kien
- Mau sac theo loai:
  - Gio/Ky: Tim nhat/Xanh navy (trang nghiem)
  - Le hoi: Do + vang
  - Sinh nhat: Vang + pastel
  - Ngay cuoi: Do + vang kim
  - Khac: Xam/Be

### Filters:
- Loc theo loai su kien
- Loc theo nhom
- Tim kiem theo ten

---

## FR-EVENT-003: Xem chi tiet su kien

**Priority:** High

### Display:
- Tat ca thong tin cua su kien
- Ngay duong lich + am lich
- Countdown: "Con X ngay Y gio"
- Lich su thong bao (neu co)
- Actions: Sua, Xoa, Chia se, Tat thong bao

---

## FR-EVENT-004: Sua su kien

**Priority:** High

### Details:
- Co the sua tat ca cac field
- Confirmation popup: "Ban co chac muon thay doi?"
- Sau khi sua -> Thong bao duoc tinh lai theo ngay moi

---

## FR-EVENT-005: Xoa su kien

**Priority:** High

### Details:
- Confirmation popup: "Ban co chac muon xoa su kien nay? Hanh dong khong the hoan tac."
- Neu su kien thuoc nhieu nhom -> Xoa khoi tat ca nhom
- Neu su kien da duoc chia se -> Khong xoa khoi app cua nguoi nhan

---

## FR-EVENT-006: Lap lai hang nam

**Priority:** Critical

### Details:
- Tat ca su kien tu dong lap lai hang nam
- Moi nam, app tu convert lai lich am -> duong cho nam do
- Doi voi thang nhuan: Dua vao option user da chon khi tao

---

## Event Type Color Scheme

| Event Type | Primary Color | Secondary Color | Hex Codes |
|------------|---------------|-----------------|-----------|
| Gio/Ky | Purple | Navy | #5E35B1, #283593 |
| Le hoi | Red | Yellow | #D32F2F, #FBC02D |
| Sinh nhat | Yellow | Pink Pastel | #FBC02D, #F48FB1 |
| Ngay cuoi | Dark Red | Gold | #C62828, #FFD54F |
| Khac | Gray | - | #9E9E9E |

---

## Related Documentation

- [Event Groups](./groups.md)
- [Lunar Calendar Conversion](./lunar-calendar.md)
- [Notification System](./notifications.md)
- [Sharing](./sharing.md)
- [Use Cases](../use-cases.md)
- [Features Index](../index.md)
