# Event Groups - Nhom Su kien

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Section:** 7.1.3 - Core Features

---

## FR-GROUP-001: Tao nhom moi

**Priority:** Medium

### Fields:
- Ten nhom (bat buoc, max 50 chars)
- Mau sac (chon tu palette)
- Icon (chon tu library)

### Examples:
- "Gia dinh noi"
- "Gia dinh ngoai"
- "Ban be"
- "Cong viec"

---

## FR-GROUP-002: Them su kien vao nhom

**Priority:** Medium

### Details:
- Mot su kien co the thuoc nhieu nhom
- Khi tao su kien moi -> Chon nhom (multi-select)
- Co the them/xoa nhom sau khi tao

---

## FR-GROUP-003: Xem su kien theo nhom

**Priority:** Medium

### Details:
- Tab "Nhom" hien thi danh sach cac nhom
- Tap vao nhom -> Hien danh sach su kien trong nhom do
- Mau sac cua su kien theo mau nhom (neu co)

---

## FR-GROUP-004: Sua/Xoa nhom

**Priority:** Medium

### Details:
- Sua ten, mau, icon
- Xoa nhom -> Confirmation: "X su kien trong nhom nay. Xoa nhom khong xoa su kien."
- Su kien van ton tai, chi bo khoi nhom

---

## Group Management UI

### Group List View
```
+----------------------------------+
| Nhom                             |
+----------------------------------+
| [Icon] Gia dinh noi     (5)  >   |
| [Icon] Gia dinh ngoai   (3)  >   |
| [Icon] Ban be           (2)  >   |
| [Icon] Cong viec        (1)  >   |
+----------------------------------+
| [+] Them nhom moi                |
+----------------------------------+
```

### Group Detail View
```
+----------------------------------+
| < Gia dinh noi           [Edit]  |
+----------------------------------+
| Su kien trong nhom:              |
|                                  |
| [Icon] Gio ong noi      15/3 AL  |
| [Icon] Gio ba noi       20/4 AL  |
| [Icon] Sinh nhat bo     10/2 AL  |
| ...                              |
+----------------------------------+
```

---

## Related Documentation

- [Event Management](./events.md)
- [Sharing](./sharing.md)
- [Use Cases](../use-cases.md)
- [Features Index](../index.md)
