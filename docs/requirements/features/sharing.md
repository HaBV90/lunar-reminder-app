# Sharing - Chia se

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Section:** 7.1.6 - Core Features

---

## FR-SHARE-001: Chia se su kien/nhom qua link

**Priority:** High

### Details:
- User chon: Chia se 1 su kien, 1 nhom, hoac tat ca
- App tao deep link: `https://lunar-app.link/share/{id}`
- Gui link qua Zalo, Messenger, SMS...

---

## FR-SHARE-002: Nhan chia se

**Priority:** High

### Details:
- Nguoi nhan tap link:
  - **Da cai app:** Mo app -> Popup xac nhan
  - **Chua cai app:** Mo landing page -> Button "Tai app" -> Sau khi cai -> Tu dong import
- Popup: "[Ten nguoi gui] muon chia se [X su kien/nhom] voi ban. Dong y?"
- Tap "Dong y" -> Su kien duoc sync vao app

---

## FR-SHARE-003: Quan ly su kien duoc chia se

**Priority:** Medium

### Details:
- Su kien duoc chia se co badge "Duoc chia se boi [Ten]"
- Nguoi nhan khong the sua, chi xem
- Nguoi nhan co the xoa khoi app cua minh (khong anh huong nguoi gui)

---

## Sharing Flow Diagram

### Sender Flow
```
1. Open event/group
2. Tap "Share" button
3. Choose share type:
   - Single event
   - Event group
   - All events
4. App generates deep link
5. Choose sharing method (Zalo, Messenger, SMS, etc.)
6. Send link
```

### Receiver Flow (Has App)
```
1. Tap shared link
2. App opens automatically
3. Show confirmation popup
4. Tap "Accept"
5. Events synced to local storage
6. Events appear in list with "Shared by" badge
```

### Receiver Flow (No App)
```
1. Tap shared link
2. Open landing page in browser
3. Show preview of shared events
4. Tap "Download App"
5. Install from App Store / Play Store
6. Open app
7. Auto-import shared events
8. Events appear in list
```

---

## Share Link Format

```
https://lunar-app.link/share/{share_id}

Where share_id contains:
- Encrypted event/group data
- Sender information
- Expiration timestamp (optional)
```

---

## Related Documentation

- [Event Management](./events.md)
- [Event Groups](./groups.md)
- [Data Sync & Backup](./data-sync.md)
- [Use Cases](../use-cases.md)
- [Features Index](../index.md)
