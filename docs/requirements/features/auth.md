# Authentication & User Management

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Section:** 7.1.1 - Core Features

---

## FR-AUTH-001: Dang nhap/Dang ky (Optional)

**Priority:** Medium

**Description:** User co the su dung app offline hoac dang nhap de sync

**Details:**
- Khong bat buoc dang nhap khi mo app lan dau
- Neu muon sync cloud -> Yeu cau dang nhap
- Hien thi message: "Dang nhap de dong bo du lieu len cloud va su dung tren nhieu thiet bi"

**Supported login methods:**
- Email + Password
- Phone number + OTP
- Google Sign-in
- Apple Sign-in (iOS only)

---

## FR-AUTH-002: Quan ly Profile

**Priority:** Medium

**Details:**
- Xem/sua thong tin ca nhan
- Doi mat khau
- Xoa tai khoan (voi confirm)

---

## Implementation Notes

### Login Flow
1. User opens app for first time
2. App shows onboarding without requiring login
3. User can use all offline features
4. When user wants to sync -> Prompt login
5. After login -> Sync local data to cloud

### Security Requirements
- JWT tokens with expiration
- Secure storage (Keychain on iOS, Keystore on Android)
- HTTPS only for all API calls

---

## Related Documentation

- [Settings Features](./settings.md)
- [Data Sync & Backup](./data-sync.md)
- [Non-Functional Requirements](../non-functional.md)
- [Features Index](../index.md)
