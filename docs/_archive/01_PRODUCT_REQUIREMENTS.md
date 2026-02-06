# Product Requirements Document (PRD)
# Lunar Reminder App - Ứng dụng Nhắc nhở Lịch Âm Việt Nam

---

**Document Version:** 1.0  
**Last Updated:** 2026-02-02  
**Status:** ✅ Approved  
**Owner:** Product Manager  

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Problem Statement](#2-problem-statement)
3. [Goals & Objectives](#3-goals--objectives)
4. [Target Users](#4-target-users)
5. [User Personas](#5-user-personas)
6. [Use Cases & Scenarios](#6-use-cases--scenarios)
7. [Functional Requirements](#7-functional-requirements)
8. [Non-Functional Requirements](#8-non-functional-requirements)
9. [User Stories](#9-user-stories)
10. [Acceptance Criteria](#10-acceptance-criteria)
11. [Out of Scope](#11-out-of-scope)
12. [Success Metrics](#12-success-metrics)
13. [Appendix](#13-appendix)

---

## 1. Executive Summary

### 1.1 Product Vision

Lunar Reminder App là ứng dụng mobile giúp công dân Việt Nam từ 12 tuổi trở lên quản lý và nhớ các sự kiện quan trọng theo lịch âm, đặc biệt là các ngày giỗ, chạp, lễ, sinh nhật, và ngày kỷ niệm. Ứng dụng tự động chuyển đổi lịch âm sang dương, gửi thông báo thông minh, và giúp người dùng không bỏ lỡ các sự kiện văn hóa truyền thống.

### 1.2 Key Features

- ✅ **Quản lý sự kiện theo lịch âm/dương**: Tạo, sửa, xóa sự kiện với lịch âm hoặc dương
- ✅ **Chuyển đổi lịch thông minh**: Tự động convert lịch âm Việt Nam ↔ lịch dương
- ✅ **Thông báo tăng dần**: Nhắc nhở với tần suất tăng khi gần đến ngày sự kiện
- ✅ **Phân loại sự kiện**: Giỗ/Chạp, Sinh nhật, Lễ hội, Ngày cưới, Khác
- ✅ **Nhóm sự kiện**: Tạo nhóm tùy chỉnh (Gia đình nội, ngoại, bạn bè...)
- ✅ **Chia sẻ**: Chia sẻ sự kiện qua link với người khác
- ✅ **Export**: Xuất sang Google Calendar hoặc lịch thiết bị
- ✅ **Widget**: Hiển thị sự kiện sắp tới trên home screen
- ✅ **Calendar View**: Xem lịch âm + dương song song
- ✅ **Tìm kiếm**: Tìm sự kiện theo tên, loại
- ✅ **Đa ngôn ngữ**: Tiếng Việt (chính), English
- ✅ **Giao diện truyền thống VN**: Màu sắc, họa tiết văn hóa Việt Nam

### 1.3 Target Platform

- **Mobile App**: iOS (13+) và Android (8+)
- **Tech**: React Native với custom UI components

### 1.4 Timeline

- **MVP Development**: 1 tuần (7 ngày)
- **Launch Target**: Tuần 2 (sau testing)

---

## 2. Problem Statement

### 2.1 Current Pain Points

**Vấn đề 1: Quên ngày giỗ, chạp**
- Nhiều bạn trẻ và gia đình không nhớ hết các ngày giỗ theo lịch âm
- Mỗi năm lịch âm chuyển sang lịch dương khác nhau → khó nhớ
- Không có công cụ chuyên biệt cho lịch âm Việt Nam

**Vấn đề 2: Lịch hiện tại không hỗ trợ lịch âm**
- Google Calendar, Apple Calendar chỉ có lịch dương
- Phải tự tính toán hoặc tra cứu lịch âm mỗi năm
- Không có thông báo thông minh theo văn hóa VN

**Vấn đề 3: Thiếu tính năng phù hợp văn hóa**
- Các app nhắc nhở chung không hiểu ngữ cảnh VN
- Không phân biệt giỗ (cần nhắc sớm) vs sinh nhật
- Không có tính năng chia sẻ cho gia đình

### 2.2 Target Problem

**Làm sao để người Việt Nam, đặc biệt là thế hệ trẻ, có thể:**
1. Dễ dàng quản lý các sự kiện theo lịch âm
2. Nhận thông báo kịp thời để chuẩn bị
3. Chia sẻ và đồng bộ với gia đình
4. Giữ gìn các giá trị văn hóa truyền thống

---

## 3. Goals & Objectives

### 3.1 Business Goals

1. **Launch MVP trong 1 tuần** với core features
2. **Đạt 1,000 users** trong tháng đầu
3. **User retention > 60%** sau 1 tháng sử dụng
4. **Positive feedback** từ community về văn hóa VN

### 3.2 User Goals

1. **Không bỏ lỡ** các ngày giỗ, chạp quan trọng
2. **Tiết kiệm thời gian** tra cứu lịch âm
3. **Dễ dàng chia sẻ** với gia đình
4. **Trải nghiệm thân thiện** với mọi lứa tuổi

### 3.3 Technical Goals

1. **Performance**: Load time < 2s
2. **Reliability**: Uptime 99%+
3. **Notification**: 100% delivery rate
4. **Offline-first**: Hoạt động không cần internet
5. **Data sync**: Đồng bộ khi có internet

---

## 4. Target Users

### 4.1 Primary Users

**Phạm vi:**
- **Độ tuổi**: 12+ tuổi
- **Địa lý**: Công dân Việt Nam (trong và ngoài nước)
- **Tech level**: Từ không biết gì đến advanced
- **Mục đích**: Nhớ ngày giỗ, chạp, sinh nhật theo lịch âm

**Đặc điểm:**
- Có smartphone (iOS hoặc Android)
- Quan tâm đến văn hóa truyền thống
- Muốn công cụ đơn giản, dễ dùng
- Có thể offline (vùng sâu vùng xa)

### 4.2 Secondary Users

- **Người già**: Cần giao diện đơn giản, chữ to
- **Người nước ngoài**: Sống ở VN, muốn hiểu văn hóa
- **Tổ chức**: Chùa, đình, làng... quản lý nhiều sự kiện

---

## 5. User Personas

### Persona 1: Minh - Bạn trẻ đô thị (25 tuổi)

**Background:**
- Làm việc tại Hà Nội
- Bận rộn, ít về quê
- Muốn nhớ ngày giỗ ông bà

**Goals:**
- Nhận thông báo trước vài ngày để sắp xếp về quê
- Chia sẻ với anh chị em để cùng nhớ
- Export sang Google Calendar để sync với lịch làm việc

**Pain Points:**
- Quên ngày giỗ vì bận
- Không biết ngày âm lịch chuyển sang dương thế nào
- Không có tool phù hợp

**Tech Savviness:** ⭐⭐⭐⭐ (Advanced)

---

### Persona 2: Cô Lan - Người nội trợ (45 tuổi)

**Background:**
- Sống tại TPHCM
- Quản lý gia đình
- Nhớ nhiều ngày giỗ của cả họ

**Goals:**
- Có tool đơn giản để quản lý tất cả ngày giỗ
- Nhắc nhở để chuẩn bị cúng sớm
- Dễ sử dụng, không phức tạp

**Pain Points:**
- Nhiều ngày giỗ, khó nhớ hết
- Viết tay trên lịch giấy → dễ quên
- Muốn chia sẻ cho con cái

**Tech Savviness:** ⭐⭐ (Basic)

---

### Persona 3: Bé An - Học sinh (14 tuổi)

**Background:**
- Học cấp 2
- Muốn nhớ sinh nhật ông bà, bố mẹ (theo âm lịch)
- Thế hệ Z, quen với smartphone

**Goals:**
- Giao diện đẹp, hiện đại
- Widget trên màn hình chính
- Dễ thêm sự kiện nhanh

**Pain Points:**
- App lịch thường xấu, khó dùng
- Không thích app phức tạp
- Muốn personalize (màu sắc, icon)

**Tech Savviness:** ⭐⭐⭐⭐ (Advanced)

---

## 6. Use Cases & Scenarios

### Use Case 1: Tạo sự kiện Ngày Giỗ

**Actor:** Cô Lan (người nội trợ)

**Precondition:**
- Đã cài app
- Đã hoàn thành onboarding

**Main Flow:**
1. Mở app → Tap nút "+" (Thêm sự kiện)
2. Chọn loại: "Giỗ/Kỵ"
3. Nhập tên: "Giỗ ông nội"
4. Chọn ngày âm lịch: 15/3 (âm lịch)
5. App tự động convert sang dương lịch năm nay
6. Chọn nhóm: "Gia đình nội"
7. Thêm ghi chú: "Cúng tại nhà"
8. Chọn nhắc trước: 3 ngày (mặc định cho giỗ)
9. Lưu → Sự kiện được tạo

**Postcondition:**
- Sự kiện xuất hiện trong danh sách
- Thông báo sẽ gửi theo lịch: 3 ngày trước, 2 ngày trước, 1 ngày trước, ngày diễn ra

**Alternative Flow:**
- A1: User chọn lịch dương thay vì âm
- A2: User không chọn nhóm (sự kiện không thuộc nhóm nào)

---

### Use Case 2: Nhận và xử lý Thông báo

**Actor:** Minh (bạn trẻ đô thị)

**Precondition:**
- Đã tạo sự kiện "Giỗ bà ngoại" vào 20/4 âm lịch
- Hôm nay là 3 ngày trước ngày giỗ

**Main Flow:**
1. **Sáng 6h30**: Nhận thông báo "Còn 3 ngày đến Giỗ bà ngoại (20/4 âm lịch = 18/5/2026)"
2. Minh tap vào notification → Mở app → Xem chi tiết sự kiện
3. Minh tap "Đóng" → Thông báo biến mất, vẫn nhận thông báo lần sau

**Alternative Flow:**
- A1: Minh tap "Tắt hết cho sự kiện này" → Không nhận notification nữa
- A2: Minh tap "Nhắc lại sau" → Snooze 1 giờ
- A3: Minh không mở notification → Vẫn nhận thông báo lần tiếp theo (11h45)

---

### Use Case 3: Chia sẻ nhóm sự kiện với gia đình

**Actor:** Cô Lan

**Precondition:**
- Đã tạo nhóm "Gia đình nội" với 5 sự kiện giỗ

**Main Flow:**
1. Vào "Nhóm" → Chọn "Gia đình nội"
2. Tap icon "Chia sẻ"
3. Chọn phương thức: "Chia sẻ qua link"
4. App tạo link: `https://lunar-app.link/share/abc123`
5. Cô Lan copy link → Gửi qua Zalo cho con gái
6. Con gái tap link → Mở app (hoặc tải app nếu chưa có)
7. Popup: "Cô Lan muốn chia sẻ nhóm 'Gia đình nội' (5 sự kiện) với bạn. Đồng ý?"
8. Con gái tap "Đồng ý" → 5 sự kiện được sync vào app của con gái

**Postcondition:**
- Con gái có đầy đủ 5 sự kiện
- Cả 2 nhận thông báo cho các sự kiện này

---

### Use Case 4: Export sang Google Calendar

**Actor:** Minh

**Precondition:**
- Minh có 10 sự kiện trong app
- Sắp hết năm (tháng 12)

**Main Flow:**
1. **3 ngày trước năm mới**: Nhận notification "Sắp sang năm mới! Export lịch sang năm sau?"
2. Minh tap notification → Mở app → Màn hình "Export"
3. Chọn "Export tất cả sang Google Calendar"
4. App yêu cầu permission → Minh cấp quyền
5. App tự động:
   - Convert 10 sự kiện âm lịch → dương lịch năm 2027
   - Tạo 10 events trong Google Calendar
   - Đặt reminder cho mỗi event
6. Hiển thị "✅ Đã export 10 sự kiện sang Google Calendar"

**Alternative Flow:**
- A1: Minh chưa cài Google Calendar → App hướng dẫn cài
- A2: Minh từ chối permission → App giải thích cần quyền để export

---

## 7. Functional Requirements

### 7.1 Core Features

#### 7.1.1 Authentication & User Management

**FR-AUTH-001: Đăng nhập/Đăng ký (Optional)**
- **Priority:** Medium
- **Description:** User có thể sử dụng app offline hoặc đăng nhập để sync
- **Details:**
  - Không bắt buộc đăng nhập khi mở app lần đầu
  - Nếu muốn sync cloud → Yêu cầu đăng nhập
  - Hiển thị message: "Đăng nhập để đồng bộ dữ liệu lên cloud và sử dụng trên nhiều thiết bị"
  
**Supported login methods:**
- Email + Password
- Phone number + OTP
- Google Sign-in
- Apple Sign-in (iOS only)

**FR-AUTH-002: Quản lý Profile**
- Xem/sửa thông tin cá nhân
- Đổi mật khẩu
- Xóa tài khoản (với confirm)

---

#### 7.1.2 Event Management - Quản lý Sự kiện

**FR-EVENT-001: Tạo sự kiện mới**
- **Priority:** Critical
- **Fields bắt buộc:**
  - Tên sự kiện (text, max 100 chars)
  - Loại sự kiện (dropdown): Giỗ/Kỵ, Sinh nhật, Lễ hội, Ngày cưới, Kỷ niệm, Khác
  - Loại lịch (radio): Âm lịch / Dương lịch
  - Ngày tháng (date picker theo loại lịch)
  
- **Fields optional:**
  - Nhóm sự kiện (dropdown, có thể tạo mới)
  - Ghi chú (textarea, max 500 chars)
  - Nhắc trước (dropdown): Theo mặc định / Tùy chỉnh
  
- **Business Rules:**
  - Nếu chọn "Giỗ/Kỵ" → Mặc định lịch âm, nhắc trước 3 ngày
  - Nếu chọn "Sinh nhật" → Mặc định lịch dương, nhắc trước 1 ngày
  - Nếu ngày âm lịch rơi vào tháng nhuận → Hiện option chọn lần 1, lần 2, hoặc cả 2
  
- **Validation:**
  - Tên không được để trống
  - Ngày phải hợp lệ (không được quá khứ cho sự kiện mới)
  - Nếu chọn "Cả 2 lần tháng nhuận" → Tạo 2 sự kiện riêng trong năm nhuận

**FR-EVENT-002: Xem danh sách sự kiện**
- **Priority:** Critical
- **Display:**
  - Sắp xếp theo thời gian (sắp tới trước)
  - Hiển thị: Tên, Ngày dương lịch, Ngày âm lịch, Còn X ngày
  - Icon phân biệt loại sự kiện
  - Màu sắc theo loại:
    - Giỗ/Kỵ: Tím nhạt/Xanh navy (trang nghiêm)
    - Lễ hội: Đỏ + vàng
    - Sinh nhật: Vàng + pastel
    - Ngày cưới: Đỏ + vàng kim
    - Khác: Xám/Be
    
- **Filters:**
  - Lọc theo loại sự kiện
  - Lọc theo nhóm
  - Tìm kiếm theo tên

**FR-EVENT-003: Xem chi tiết sự kiện**
- **Priority:** High
- **Display:**
  - Tất cả thông tin của sự kiện
  - Ngày dương lịch + âm lịch
  - Countdown: "Còn X ngày Y giờ"
  - Lịch sử thông báo (nếu có)
  - Actions: Sửa, Xóa, Chia sẻ, Tắt thông báo

**FR-EVENT-004: Sửa sự kiện**
- **Priority:** High
- **Details:**
  - Có thể sửa tất cả các field
  - Confirmation popup: "Bạn có chắc muốn thay đổi?"
  - Sau khi sửa → Thông báo được tính lại theo ngày mới

**FR-EVENT-005: Xóa sự kiện**
- **Priority:** High
- **Details:**
  - Confirmation popup: "Bạn có chắc muốn xóa sự kiện này? Hành động không thể hoàn tác."
  - Nếu sự kiện thuộc nhiều nhóm → Xóa khỏi tất cả nhóm
  - Nếu sự kiện đã được chia sẻ → Không xóa khỏi app của người nhận

**FR-EVENT-006: Lặp lại hàng năm**
- **Priority:** Critical
- **Details:**
  - Tất cả sự kiện tự động lặp lại hàng năm
  - Mỗi năm, app tự convert lại lịch âm → dương cho năm đó
  - Đối với tháng nhuận: Dựa vào option user đã chọn khi tạo

---

#### 7.1.3 Event Groups - Nhóm Sự kiện

**FR-GROUP-001: Tạo nhóm mới**
- **Priority:** Medium
- **Fields:**
  - Tên nhóm (bắt buộc, max 50 chars)
  - Màu sắc (chọn từ palette)
  - Icon (chọn từ library)
  
- **Examples:**
  - "Gia đình nội"
  - "Gia đình ngoại"
  - "Bạn bè"
  - "Công việc"

**FR-GROUP-002: Thêm sự kiện vào nhóm**
- **Details:**
  - Một sự kiện có thể thuộc nhiều nhóm
  - Khi tạo sự kiện mới → Chọn nhóm (multi-select)
  - Có thể thêm/xóa nhóm sau khi tạo

**FR-GROUP-003: Xem sự kiện theo nhóm**
- **Priority:** Medium
- **Details:**
  - Tab "Nhóm" hiển thị danh sách các nhóm
  - Tap vào nhóm → Hiện danh sách sự kiện trong nhóm đó
  - Màu sắc của sự kiện theo màu nhóm (nếu có)

**FR-GROUP-004: Sửa/Xóa nhóm**
- **Details:**
  - Sửa tên, màu, icon
  - Xóa nhóm → Confirmation: "X sự kiện trong nhóm này. Xóa nhóm không xóa sự kiện."
  - Sự kiện vẫn tồn tại, chỉ bỏ khỏi nhóm

---

#### 7.1.4 Lunar Calendar Conversion - Chuyển đổi Lịch Âm

**FR-LUNAR-001: Chuyển đổi âm → dương**
- **Priority:** Critical
- **Details:**
  - Sử dụng thuật toán lịch âm Việt Nam (khác Trung Quốc)
  - Tính toán chính xác theo múi giờ Việt Nam (GMT+7)
  - Xử lý tháng nhuận:
    - Phát hiện năm nhuận
    - Cho phép user chọn lần 1, 2, hoặc cả 2
    - Thông báo rõ: "Tháng 4 nhuận - Lần 1" hoặc "Lần 2"

**FR-LUNAR-002: Hiển thị song song**
- **Priority:** Medium
- **Details:**
  - Mọi nơi hiện ngày → Hiển thị cả dương và âm
  - Format: "18/05/2026 (20/4 âm lịch)"
  - Calendar view: Ngày âm hiện dưới ngày dương

**FR-LUNAR-003: Validation ngày âm lịch**
- **Details:**
  - Tháng: 1-12 (có thể nhuận)
  - Ngày: 1-30 (tháng thiếu có thể 29 ngày)
  - Validate theo thuật toán lịch âm

---

#### 7.1.5 Notification System - Hệ thống Thông báo

**FR-NOTIF-001: Kịch bản thông báo mặc định**
- **Priority:** Critical

**Cho Giỗ/Chạp (nhắc trước 3 ngày):**
- **3 ngày trước:** 2 lần/ngày (6h30, 18h30)
- **2 ngày trước:** 4 lần/ngày (6h30, 11h45, 18h30, 20h30)
- **1 ngày trước:** 4 lần/ngày (6h30, 11h45, 18h30, 20h30)
- **Ngày diễn ra:** 4 lần/ngày (6h30, 11h45, 18h30, 20h30)

**Cho Sinh nhật/Lễ/Ngày cưới (nhắc trước 1 ngày):**
- **1 ngày trước:** 2 lần/ngày (6h30, 18h30)
- **Ngày diễn ra:** 4 lần/ngày (6h30, 11h45, 18h30, 20h30)

**FR-NOTIF-002: Tùy chỉnh giờ thông báo**
- **Priority:** Medium
- **Details:**
  - User có thể thay đổi 4 khung giờ mặc định
  - Setting global cho tất cả sự kiện
  - Có thể điều chỉnh từng slot:
    - Slot 1: Sáng (mặc định 6h30)
    - Slot 2: Trưa (mặc định 11h45)
    - Slot 3: Tối (mặc định 18h30)
    - Slot 4: Tối muộn (mặc định 20h30)

**FR-NOTIF-003: Tùy chỉnh số lần thông báo**
- **Priority:** Low
- **Details:**
  - User có thể giảm số lần thông báo
  - Ví dụ: Chỉ 2 lần/ngày thay vì 4 lần
  - Chọn các slot nào muốn nhận

**FR-NOTIF-004: Nội dung thông báo**
- **Format:**
  - Title: "Còn X ngày đến [Tên sự kiện]"
  - Body: "[Ngày dương lịch] ([Ngày âm lịch])"
  - Action: Tap → Mở app → Xem chi tiết sự kiện
  
- **Example:**
  - Title: "Còn 3 ngày đến Giỗ ông nội"
  - Body: "18/05/2026 (20/4 âm lịch)"

**FR-NOTIF-005: Xử lý thông báo**
- **Details:**
  - **Nút "Đóng"**: Tắt lần này, vẫn nhận lần sau
  - **Nút "Tắt hết"**: Không nhận nữa cho sự kiện này
  - **Nút "Nhắc lại sau"**: Snooze 1 giờ
  - Trong app: Toggle on/off thông báo cho từng sự kiện

**FR-NOTIF-006: Notification Platform**
- **Priority:** Critical
- **Platform:** OneSignal
- **Details:**
  - Push notification đến iOS và Android
  - Scheduled notifications (lên lịch trước)
  - Track delivery rate

---

#### 7.1.6 Sharing - Chia sẻ

**FR-SHARE-001: Chia sẻ sự kiện/nhóm qua link**
- **Priority:** High
- **Details:**
  - User chọn: Chia sẻ 1 sự kiện, 1 nhóm, hoặc tất cả
  - App tạo deep link: `https://lunar-app.link/share/{id}`
  - Gửi link qua Zalo, Messenger, SMS...
  
**FR-SHARE-002: Nhận chia sẻ**
- **Details:**
  - Người nhận tap link:
    - **Đã cài app:** Mở app → Popup xác nhận
    - **Chưa cài app:** Mở landing page → Button "Tải app" → Sau khi cài → Tự động import
  - Popup: "[Tên người gửi] muốn chia sẻ [X sự kiện/nhóm] với bạn. Đồng ý?"
  - Tap "Đồng ý" → Sự kiện được sync vào app

**FR-SHARE-003: Quản lý sự kiện được chia sẻ**
- **Details:**
  - Sự kiện được chia sẻ có badge "Được chia sẻ bởi [Tên]"
  - Người nhận không thể sửa, chỉ xem
  - Người nhận có thể xóa khỏi app của mình (không ảnh hưởng người gửi)

---

#### 7.1.7 Calendar Integration - Tích hợp Lịch

**FR-CAL-001: Export sang Google Calendar**
- **Priority:** Medium
- **Details:**
  - Export tất cả sự kiện của năm tiếp theo
  - Chuyển đổi lịch âm → dương
  - Tạo sự kiện với reminder trong Google Calendar
  - Sự kiện được đánh dấu "repeat yearly"

**FR-CAL-002: Export sang lịch thiết bị**
- **Details:**
  - iOS: Export vào Apple Calendar
  - Android: Export vào Google Calendar hoặc calendar mặc định
  - Tương tự FR-CAL-001

**FR-CAL-003: Nhắc export cuối năm**
- **Priority:** Medium
- **Details:**
  - Vào tháng 12, 3 ngày trước năm mới
  - Notification: "Sắp sang năm mới! Export lịch sang năm sau?"
  - Tần suất: Tương tự thông báo giỗ (2-4 lần/ngày)
  - Có nút quick action để export ngay

**FR-CAL-004: Thêm vào app nhắc nhở**
- **Priority:** Low
- **Details:**
  - Export sang Reminders (iOS) hoặc Google Tasks (Android)
  - Format tương tự

---

#### 7.1.8 Calendar View - Xem Lịch

**FR-CALVIEW-001: Lịch tháng (Optional)**
- **Priority:** Low
- **Display:**
  - Lịch dương làm chính (grid view)
  - Mỗi ô ngày dương có ngày âm bên dưới (nhỏ hơn)
  - Sự kiện hiển thị bằng icon nhỏ dưới ngày
  
- **Interaction:**
  - Tap vào ngày → Hiện danh sách sự kiện của ngày đó (bottom sheet)
  - Tap vào icon sự kiện → Xem chi tiết sự kiện

**FR-CALVIEW-002: Điều hướng**
- **Details:**
  - Swipe left/right để chuyển tháng
  - Button "Hôm nay" để về ngày hiện tại
  - Hiển thị tên tháng + năm

---

#### 7.1.9 Search - Tìm kiếm

**FR-SEARCH-001: Tìm kiếm sự kiện**
- **Priority:** Medium
- **Search by:**
  - Tên sự kiện (fuzzy search)
  - Loại sự kiện (Giỗ, Sinh nhật...)
  - Nhóm
  
- **Display:**
  - Kết quả realtime khi gõ
  - Highlight keyword
  - Sắp xếp theo relevance

---

#### 7.1.10 Widget

**FR-WIDGET-001: Home screen widget**
- **Priority:** Medium
- **Kích thước:**
  - **Nhỏ (2x2):** 1 sự kiện gần nhất + countdown
  - **Vừa (4x2):** 2-3 sự kiện
  - **Lớn (4x4):** Mini calendar + 3-4 sự kiện

**FR-WIDGET-002: Nội dung hiển thị**
- **Details:**
  - Tên sự kiện
  - Icon loại sự kiện
  - Countdown: "Còn X ngày"
  - Ngày dương lịch + âm lịch

**FR-WIDGET-003: Tương tác**
- **Details:**
  - Tap vào widget → Mở app
  - Nếu 1 ngày có 1 sự kiện: Tap icon → Xem chi tiết
  - Nếu nhiều sự kiện: Tap → Mở app (danh sách ngày đó)

**FR-WIDGET-004: Cập nhật**
- **Details:**
  - Auto refresh mỗi 1 giờ
  - Update ngay khi có thay đổi trong app

---

#### 7.1.11 Settings - Cài đặt

**FR-SETTING-001: Notification settings**
- Bật/tắt thông báo toàn bộ app
- Tùy chỉnh khung giờ thông báo
- Tùy chỉnh số lần thông báo

**FR-SETTING-002: App settings**
- Ngôn ngữ (Tiếng Việt / English)
- Theme (Light / Dark / Auto)
- Màu sắc giao diện

**FR-SETTING-003: Account settings**
- Xem thông tin tài khoản
- Đổi mật khẩu
- Đăng xuất
- Xóa tài khoản

**FR-SETTING-004: Data management**
- Export dữ liệu (JSON)
- Import dữ liệu (JSON)
- Xóa toàn bộ dữ liệu (với confirm)

**FR-SETTING-005: About**
- Version app
- Privacy Policy
- Terms of Service
- Contact support

---

#### 7.1.12 Onboarding

**FR-ONBOARD-001: Welcome screens**
- **Priority:** High
- **Screens:**
  1. Welcome: Giới thiệu app + logo
  2. Features: 3-4 tính năng chính (swipeable)
  3. Permissions: Xin quyền Notification
  4. Optional Login: "Đăng nhập để sync" (có nút Skip)

**FR-ONBOARD-002: Tutorial**
- **Details:**
  - Hướng dẫn tạo sự kiện đầu tiên
  - Có thể skip
  - Có thể xem lại trong Settings → "Hướng dẫn sử dụng"

---

#### 7.1.13 Data Sync & Backup

**FR-SYNC-001: Cloud sync (khi đăng nhập)**
- **Priority:** High
- **Details:**
  - Lưu trữ: PostgreSQL trên server
  - Sync 2 chiều: Local ↔ Cloud
  - Conflict resolution: Last write wins
  - Sync khi: Mở app, thêm/sửa/xóa sự kiện, mỗi 5 phút (nếu online)

**FR-SYNC-002: Offline-first**
- **Priority:** Critical
- **Details:**
  - App hoạt động đầy đủ khi offline
  - Dữ liệu lưu local (SQLite hoặc Realm)
  - Khi online → Tự động sync

**FR-SYNC-003: Multi-device sync**
- **Details:**
  - User đăng nhập trên nhiều thiết bị
  - Dữ liệu sync realtime
  - Notification gửi đến tất cả thiết bị

**FR-SYNC-004: Export/Import**
- **Details:**
  - Export: JSON file chứa tất cả sự kiện
  - Import: Đọc JSON → Validate → Import
  - Có option merge hoặc replace

---

### 7.2 UI/UX Requirements

#### 7.2.1 Design System

**Color Palette:**
- **Primary:** Đỏ truyền thống (#D32F2F, #C62828)
- **Secondary:** Vàng kim (#FBC02D, #F9A825)
- **Background:** Be/Đỏ nhạt (#FFF8E1, #FFEBEE)
- **Text:** #212121, #757575
- **Event Colors:**
  - Giỗ/Kỵ: #5E35B1 (Tím), #283593 (Xanh navy)
  - Lễ hội: #D32F2F (Đỏ) + #FBC02D (Vàng)
  - Sinh nhật: #FBC02D (Vàng) + #F48FB1 (Hồng pastel)
  - Ngày cưới: #C62828 (Đỏ đậm) + #FFD54F (Vàng kim)
  - Khác: #9E9E9E (Xám)

**Typography:**
- **Font family:** 
  - iOS: SF Pro (system)
  - Android: Roboto (system)
  - Tiếng Việt: Hỗ trợ dấu tiếng Việt
- **Sizes:**
  - Heading 1: 28px, Bold
  - Heading 2: 24px, Semi-bold
  - Body: 16px, Regular
  - Caption: 14px, Regular

**Icons:**
- Icon library: Custom icons + React Native Vector Icons
- Motifs: Mây, rồng, hoa sen, hoa văn trống đồng, đèn lồng...

**Spacing:**
- Base unit: 8px
- Margin/Padding: 8, 16, 24, 32px

#### 7.2.2 Accessibility

**FR-ACCESS-001: Font scaling**
- Hỗ trợ Dynamic Type (iOS) và Font scaling (Android)
- Text phải scale được từ 100% → 200%

**FR-ACCESS-002: Color contrast**
- Ratio tối thiểu: 4.5:1 (WCAG AA)
- Text trên background phải đủ contrast

**FR-ACCESS-003: Touch targets**
- Minimum size: 44x44 points (iOS), 48x48 dp (Android)

**FR-ACCESS-004: Screen readers**
- Tất cả elements có accessibility labels
- Hỗ trợ VoiceOver (iOS) và TalkBack (Android)

---

### 7.3 Localization - Đa ngôn ngữ

**FR-I18N-001: Supported languages**
- **Primary:** Tiếng Việt (vi-VN)
- **Secondary:** English (en-US)

**FR-I18N-002: Translation coverage**
- 100% UI text
- Error messages
- Notification text
- Onboarding content

**FR-I18N-003: Date/Time formatting**
- Theo locale của device
- VN: DD/MM/YYYY
- EN: MM/DD/YYYY

**FR-I18N-004: Terminology**
- "Lịch âm" / "Lunar calendar"
- "Lịch dương" / "Solar calendar"
- "Giỗ" / "Death anniversary"
- Etc.

---

### 7.4 Performance Requirements

**FR-PERF-001: App load time**
- Cold start: < 2s
- Warm start: < 1s

**FR-PERF-002: Screen transition**
- 60fps smooth animation
- No janky scroll

**FR-PERF-003: Memory usage**
- < 100MB RAM typical
- < 150MB peak

**FR-PERF-004: Battery**
- Optimize notification scheduling
- Background tasks minimal

**FR-PERF-005: Network**
- Optimize API calls (batch, cache)
- Work offline

---

## 8. Non-Functional Requirements

### 8.1 Security

**NFR-SEC-001: Authentication**
- JWT tokens với expiration
- Secure storage (Keychain/Keystore)
- HTTPS only

**NFR-SEC-002: Data encryption**
- Sensitive data encrypted at rest
- TLS 1.3 for network

**NFR-SEC-003: API security**
- Rate limiting
- Input validation
- SQL injection prevention

### 8.2 Privacy

**NFR-PRIV-001: Data collection**
- Chỉ thu thập data cần thiết
- Không bán data cho bên thứ 3

**NFR-PRIV-002: GDPR compliance**
- User có quyền xóa data
- Export data theo yêu cầu

### 8.3 Reliability

**NFR-REL-001: Uptime**
- Target: 99% uptime
- Downtime tối đa: 7 giờ/tháng

**NFR-REL-002: Data integrity**
- Backup hàng ngày
- Transaction atomicity

**NFR-REL-003: Error handling**
- Graceful degradation
- User-friendly error messages

### 8.4 Scalability

**NFR-SCALE-001: User capacity**
- Phase 1 (Local): 1,000-5,000 users
- Phase 2 (Cloud): 10,000-100,000 users

**NFR-SCALE-002: Database**
- PostgreSQL với indexing
- Redis caching

### 8.5 Maintainability

**NFR-MAINT-001: Code quality**
- Test coverage > 80%
- Linting (ESLint)
- Type safety (TypeScript)

**NFR-MAINT-002: Documentation**
- Code comments
- API docs (OpenAPI)
- Architecture diagrams

---

## 9. User Stories

### Epic 1: Event Management

**US-1.1:** As a user, I want to create a new event with lunar date so that I can remember death anniversaries.
- **Acceptance Criteria:**
  - Can select "Giỗ/Kỵ" as event type
  - Can input lunar date (day, month)
  - App converts to solar date automatically
  - Event appears in list sorted by upcoming date

**US-1.2:** As a user, I want to edit an event so that I can fix mistakes.
- **Acceptance Criteria:**
  - Can tap event to view details
  - Can tap "Edit" button
  - Can modify all fields
  - Changes saved after confirmation

**US-1.3:** As a user, I want to delete an event so that I can remove outdated information.
- **Acceptance Criteria:**
  - Can tap "Delete" button
  - System shows confirmation dialog
  - Event removed from list after confirm
  - Cannot undo deletion

---

### Epic 2: Notifications

**US-2.1:** As a user, I want to receive notifications before events so that I can prepare in advance.
- **Acceptance Criteria:**
  - Receive notification 3 days before for death anniversaries
  - Receive notification 1 day before for birthdays
  - Notification shows event name, date, countdown

**US-2.2:** As a user, I want to customize notification times so that I receive alerts when convenient.
- **Acceptance Criteria:**
  - Can access notification settings
  - Can modify 4 time slots
  - Changes apply to all future notifications

**US-2.3:** As a user, I want to turn off notifications for specific events so that I don't get disturbed.
- **Acceptance Criteria:**
  - Can toggle notification on/off in event details
  - No notifications sent when off
  - Event still visible in list

---

### Epic 3: Sharing

**US-3.1:** As a user, I want to share events with family so that everyone can remember together.
- **Acceptance Criteria:**
  - Can tap "Share" button
  - App generates shareable link
  - Recipient receives events after accepting

**US-3.2:** As a user, I want to receive shared events from others so that I stay updated.
- **Acceptance Criteria:**
  - Can tap shared link
  - App shows preview of events
  - Can accept or decline
  - Accepted events appear in my list

---

### Epic 4: Export

**US-4.1:** As a user, I want to export events to Google Calendar so that I see them in my main calendar.
- **Acceptance Criteria:**
  - Can tap "Export" in settings
  - System converts lunar to solar dates
  - Events created in Google Calendar
  - Success message shown

---

## 10. Acceptance Criteria

### Global Acceptance Criteria (All Features)

1. **Functionality:**
   - Feature works as described in requirements
   - No critical bugs
   - Edge cases handled

2. **UI/UX:**
   - Follows design system
   - Responsive on different screen sizes
   - Smooth animations (60fps)
   - Accessible (screen readers, font scaling)

3. **Code Quality:**
   - TypeScript types defined
   - Unit tests written (coverage > 80%)
   - Code reviewed and approved
   - No ESLint errors

4. **Documentation:**
   - Code comments for complex logic
   - API endpoints documented (if backend)
   - User-facing help text clear

5. **Performance:**
   - No memory leaks
   - Load time < 2s
   - Works offline (for offline features)

6. **Cross-platform:**
   - Works on both iOS and Android
   - Consistent behavior
   - Platform-specific UI where appropriate

---

## 11. Out of Scope

### Phase 1 (MVP) - Not Included:

1. **Social Features:**
   - User profiles with photos
   - Following other users
   - Public event feeds
   - Comments on events

2. **Advanced Calendar:**
   - Sync with Outlook, iCal
   - Calendar invite via email
   - Recurring events with complex patterns

3. **Gamification:**
   - Achievements, badges
   - Streak tracking
   - Leaderboards

4. **AI Features:**
   - Smart event suggestions
   - Auto-categorization
   - Predictive notifications

5. **Payment:**
   - Premium subscription
   - In-app purchases
   - Ads

6. **Advanced Sharing:**
   - Collaborative editing
   - Real-time sync during share
   - Event comments/chat

7. **Widget Advanced:**
   - Interactive widgets
   - Widget configuration
   - Multiple widget themes

8. **Reports & Analytics:**
   - Event statistics
   - Usage analytics
   - Export reports

### Potential Future Phases:

- Phase 2: Social features, premium tier
- Phase 3: AI suggestions, advanced analytics
- Phase 4: Web app, desktop app

---

## 12. Success Metrics

### 12.1 Product Metrics

**User Acquisition:**
- Target: 1,000 users in Month 1
- Target: 5,000 users in Month 3

**Engagement:**
- Daily Active Users (DAU): > 30%
- Weekly Active Users (WAU): > 60%
- Monthly Active Users (MAU): > 80%
- Average session duration: > 2 minutes

**Retention:**
- Day 1 retention: > 50%
- Day 7 retention: > 40%
- Day 30 retention: > 60%

**Feature Usage:**
- Events created per user: > 5
- Notifications opened: > 70%
- Sharing feature used: > 20% of users
- Export feature used: > 10% of users

### 12.2 Technical Metrics

**Performance:**
- App load time: < 2s (95th percentile)
- API response time: < 500ms (95th percentile)
- Crash-free rate: > 99%

**Quality:**
- Critical bugs: 0
- High priority bugs: < 3
- Test coverage: > 80%

**Notifications:**
- Delivery rate: > 95%
- Open rate: > 50%

### 12.3 Business Metrics

**Cost:**
- Development cost: Within budget
- Server cost: < $100/month (Phase 1)

**Revenue (Future):**
- Conversion to premium: > 5%
- ARPU (Average Revenue Per User): > $2/month

---

## 13. Appendix

### 13.1 Glossary

- **Lịch âm (Lunar calendar):** Hệ thống lịch dựa trên chu kỳ mặt trăng, được sử dụng truyền thống tại Việt Nam
- **Lịch dương (Solar calendar):** Hệ thống lịch dựa trên chu kỳ mặt trời (Gregorian calendar)
- **Giỗ/Kỵ:** Ngày kỷ niệm người thân qua đời
- **Chạp:** Tháng 12 âm lịch
- **Tháng nhuận:** Tháng thêm trong năm nhuận âm lịch (xảy ra 7 lần trong 19 năm)
- **MVP:** Minimum Viable Product - Phiên bản sản phẩm tối thiểu khả dụng

### 13.2 References

- Vietnamese Lunar Calendar Algorithm: [To be added]
- React Native Documentation: https://reactnative.dev
- NestJS Documentation: https://nestjs.com
- OneSignal Documentation: https://documentation.onesignal.com

### 13.3 Document History

| Version | Date       | Author | Changes               |
|---------|------------|--------|-----------------------|
| 1.0     | 2026-02-02 | PM     | Initial complete PRD  |

---

**End of Product Requirements Document**

**Next Steps:**
1. Review and approve PRD
2. Create Technical Specification
3. Design UI/UX mockups
4. Break down into tasks
5. Start development

