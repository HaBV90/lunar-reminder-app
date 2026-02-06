# Use Cases & Scenarios

**Document Version:** 1.0
**Last Updated:** 2026-02-02

---

## 6. Use Cases & Scenarios

### Use Case 1: Tao su kien Ngay Gio

**Actor:** Co Lan (nguoi noi tro)

**Precondition:**
- Da cai app
- Da hoan thanh onboarding

**Main Flow:**
1. Mo app -> Tap nut "+" (Them su kien)
2. Chon loai: "Gio/Ky"
3. Nhap ten: "Gio ong noi"
4. Chon ngay am lich: 15/3 (am lich)
5. App tu dong convert sang duong lich nam nay
6. Chon nhom: "Gia dinh noi"
7. Them ghi chu: "Cung tai nha"
8. Chon nhac truoc: 3 ngay (mac dinh cho gio)
9. Luu -> Su kien duoc tao

**Postcondition:**
- Su kien xuat hien trong danh sach
- Thong bao se gui theo lich: 3 ngay truoc, 2 ngay truoc, 1 ngay truoc, ngay dien ra

**Alternative Flow:**
- A1: User chon lich duong thay vi am
- A2: User khong chon nhom (su kien khong thuoc nhom nao)

---

### Use Case 2: Nhan va xu ly Thong bao

**Actor:** Minh (ban tre do thi)

**Precondition:**
- Da tao su kien "Gio ba ngoai" vao 20/4 am lich
- Hom nay la 3 ngay truoc ngay gio

**Main Flow:**
1. **Sang 6h30**: Nhan thong bao "Con 3 ngay den Gio ba ngoai (20/4 am lich = 18/5/2026)"
2. Minh tap vao notification -> Mo app -> Xem chi tiet su kien
3. Minh tap "Dong" -> Thong bao bien mat, van nhan thong bao lan sau

**Alternative Flow:**
- A1: Minh tap "Tat het cho su kien nay" -> Khong nhan notification nua
- A2: Minh tap "Nhac lai sau" -> Snooze 1 gio
- A3: Minh khong mo notification -> Van nhan thong bao lan tiep theo (11h45)

---

### Use Case 3: Chia se nhom su kien voi gia dinh

**Actor:** Co Lan

**Precondition:**
- Da tao nhom "Gia dinh noi" voi 5 su kien gio

**Main Flow:**
1. Vao "Nhom" -> Chon "Gia dinh noi"
2. Tap icon "Chia se"
3. Chon phuong thuc: "Chia se qua link"
4. App tao link: `https://lunar-app.link/share/abc123`
5. Co Lan copy link -> Gui qua Zalo cho con gai
6. Con gai tap link -> Mo app (hoac tai app neu chua co)
7. Popup: "Co Lan muon chia se nhom 'Gia dinh noi' (5 su kien) voi ban. Dong y?"
8. Con gai tap "Dong y" -> 5 su kien duoc sync vao app cua con gai

**Postcondition:**
- Con gai co day du 5 su kien
- Ca 2 nhan thong bao cho cac su kien nay

---

### Use Case 4: Export sang Google Calendar

**Actor:** Minh

**Precondition:**
- Minh co 10 su kien trong app
- Sap het nam (thang 12)

**Main Flow:**
1. **3 ngay truoc nam moi**: Nhan notification "Sap sang nam moi! Export lich sang nam sau?"
2. Minh tap notification -> Mo app -> Man hinh "Export"
3. Chon "Export tat ca sang Google Calendar"
4. App yeu cau permission -> Minh cap quyen
5. App tu dong:
   - Convert 10 su kien am lich -> duong lich nam 2027
   - Tao 10 events trong Google Calendar
   - Dat reminder cho moi event
6. Hien thi "Da export 10 su kien sang Google Calendar"

**Alternative Flow:**
- A1: Minh chua cai Google Calendar -> App huong dan cai
- A2: Minh tu choi permission -> App giai thich can quyen de export

---

## Use Case Summary

| Use Case | Primary Actor | Priority | Related Feature |
|----------|--------------|----------|-----------------|
| Create Gio Event | Co Lan | Critical | Event Management |
| Handle Notification | Minh | Critical | Notifications |
| Share Event Group | Co Lan | High | Sharing |
| Export to Calendar | Minh | Medium | Calendar Integration |

---

## Related Documentation

- [Product Overview](./overview.md)
- [Target Users & Personas](./personas.md)
- [Event Management Features](./features/events.md)
- [Notification Features](./features/notifications.md)
- [Sharing Features](./features/sharing.md)
- [Calendar Integration](./features/calendar-integration.md)
- [Requirements Index](./index.md)
