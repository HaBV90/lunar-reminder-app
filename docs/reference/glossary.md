# Glossary - Bảng Thuật ngữ
# Lunar Reminder App

---

**Last Updated:** 2026-02-02

This document defines all terms, acronyms, and concepts used throughout the project documentation.

---

## Table of Contents

1. [Vietnamese Cultural Terms](#vietnamese-cultural-terms)
2. [Calendar & Date Terms](#calendar--date-terms)
3. [Technical Terms](#technical-terms)
4. [Product Terms](#product-terms)
5. [Development Terms](#development-terms)

---

## Vietnamese Cultural Terms

### Giỗ / Ngày giỗ
**English:** Death anniversary, Memorial day  
**Definition:** Ngày kỷ niệm người thân qua đời, thường được tổ chức hàng năm theo lịch âm. Gia đình cúng bái, tưởng nhớ người đã khuất.  
**Example:** "Ngày giỗ ông nội là 15/3 âm lịch"

### Kỵ
**English:** Death anniversary (formal term)  
**Definition:** Từ trang trọng hơn của "giỗ", thường dùng trong văn bản chính thức.  
**Same as:** Giỗ

### Chạp
**English:** Twelfth lunar month  
**Definition:** Tháng 12 âm lịch, tháng cuối cùng của năm âm lịch. Thường có nhiều hoạt động chuẩn bị Tết.  
**Example:** "Tháng Chạp là thời điểm chuẩn bị Tết Nguyên Đán"

### Tết / Tết Nguyên Đán
**English:** Lunar New Year, Vietnamese New Year  
**Definition:** Ngày đầu tiên của năm âm lịch (1/1 âm lịch), là ngày lễ lớn nhất của người Việt.  
**Example:** "Tết năm 2026 rơi vào ngày 17/02/2026 dương lịch"

### Rằm
**English:** Full moon day (15th of lunar month)  
**Definition:** Ngày 15 âm lịch, ngày trăng tròn. Một số ngày rằm có ý nghĩa đặc biệt (Rằm tháng Giêng, Rằm tháng 7...)  
**Example:** "Rằm tháng 7 (Vu Lan) là ngày báo hiếu"

### Mùng 1
**English:** First day of lunar month  
**Definition:** Ngày đầu tiên của tháng âm lịch.  
**Example:** "Mùng 1 Tết"

---

## Calendar & Date Terms

### Lịch âm
**English:** Lunar calendar  
**Definition:** Hệ thống lịch dựa trên chu kỳ mặt trăng (moon phases). Một tháng âm lịch = 1 chu kỳ trăng (29-30 ngày). Việt Nam sử dụng lịch âm Việt Nam (dựa trên lịch âm Trung Quốc có điều chỉnh theo múi giờ VN).  
**Technical:** Lunisolar calendar based on moon phases and solar year  
**Note:** Khác với lịch âm Trung Quốc do sự khác biệt múi giờ → một số ngày có thể khác 1 ngày

### Lịch dương
**English:** Solar calendar, Gregorian calendar  
**Definition:** Hệ thống lịch dựa trên chu kỳ mặt trời quay quanh trái đất. Lịch phổ biến trên thế giới hiện nay.  
**Technical:** Gregorian calendar with 365/366 days per year  
**Example:** "18/05/2026 là ngày dương lịch"

### Năm nhuận âm lịch
**English:** Leap year (lunar calendar)  
**Definition:** Năm có 13 tháng thay vì 12 tháng. Xảy ra 7 lần trong 19 năm để đồng bộ với lịch dương.  
**How it works:** Một tháng được "nhân đôi" (tháng nhuận)  
**Example:** "Năm 2023 có tháng 2 nhuận (2 lần tháng 2 âm lịch)"

### Tháng nhuận
**English:** Leap month, Intercalary month  
**Definition:** Tháng thêm vào trong năm nhuận âm lịch. Có 2 lần cùng số tháng trong năm.  
**Notation:** Tháng X nhuận (ví dụ: Tháng 2 nhuận)  
**User choice:** User có thể chọn tổ chức sự kiện vào lần 1, lần 2, hoặc cả 2 lần

### Chuyển đổi lịch âm-dương
**English:** Lunar-to-solar conversion  
**Definition:** Quá trình tính toán chuyển một ngày từ lịch âm sang lịch dương hoặc ngược lại.  
**Technical:** Using Vietnamese lunar calendar algorithm with GMT+7 timezone  
**Example:** "20/4 âm lịch năm 2026 = 18/05/2026 dương lịch"

### Múi giờ Việt Nam
**English:** Vietnam timezone  
**Definition:** GMT+7 (UTC+7), không có daylight saving time.  
**Importance:** Ảnh hưởng đến việc tính toán lịch âm (giờ đầu ngày khác Trung Quốc)

---

## Technical Terms

### MVP (Minimum Viable Product)
**Vietnamese:** Sản phẩm khả dụng tối thiểu  
**Definition:** Phiên bản đầu tiên của sản phẩm với chỉ các tính năng cốt lõi, đủ để launch và thu thập feedback từ users.  
**For this project:** Core features trong 1 tuần, không bao gồm advanced features

### React Native
**Definition:** Framework để xây dựng mobile app (iOS & Android) bằng JavaScript/TypeScript và React.  
**Why chosen:** Cross-platform, fast development, large community

### NestJS
**Definition:** Progressive Node.js framework để xây dựng server-side applications, dựa trên TypeScript.  
**Why chosen:** Structured, scalable, TypeScript-first, great for APIs

### Redux Toolkit
**Definition:** Official toolset for Redux state management, simplified API.  
**Why chosen:** Industry standard, powerful, great DevTools, TypeScript support

### PostgreSQL
**Definition:** Open-source relational database (SQL).  
**Why chosen:** Reliable, ACID compliant, great for structured data (events, users...)

### OneSignal
**Definition:** Push notification service provider.  
**Why chosen:** Free tier, easy integration, supports iOS & Android, rich features

### Cloudflare Tunnel
**Definition:** Service to expose local server to internet securely without port forwarding.  
**Why chosen:** Free, stable, secure, no need for public IP or cloud hosting initially  
**Use case:** Development, staging, và "production" tạm thời (1-5K users)

### JWT (JSON Web Token)
**Definition:** Standard for authentication tokens, contains user info và signature.  
**Use case:** User login, API authentication

### API (Application Programming Interface)
**Definition:** Interface for communication between frontend (app) và backend (server).  
**This project:** RESTful API with JSON payloads

### Offline-first
**Definition:** Design pattern where app hoạt động đầy đủ khi offline, sync data khi online.  
**Why important:** User ở vùng sâu vùng xa, kết nối internet không ổn định

### Local storage / SQLite
**Definition:** Database trên thiết bị mobile để lưu data locally.  
**Use case:** Events, settings, user data when offline

---

## Product Terms

### Event (Sự kiện)
**Definition:** Một mục trong lịch đại diện cho một ngày đặc biệt (giỗ, sinh nhật, lễ...)  
**Attributes:**
- Name (Tên)
- Type (Loại: Giỗ, Sinh nhật, Lễ, Ngày cưới, Khác)
- Date (Ngày: âm lịch hoặc dương lịch)
- Group (Nhóm)
- Notes (Ghi chú)
- Notification settings (Cài đặt thông báo)

### Event Type (Loại sự kiện)
**Definition:** Phân loại sự kiện theo mục đích, ảnh hưởng đến màu sắc và cách nhắc nhở.  
**Types:**
1. **Giỗ/Kỵ:** Death anniversary (nhắc trước 3 ngày, tần suất cao)
2. **Sinh nhật:** Birthday (nhắc trước 1 ngày)
3. **Lễ hội:** Festival/Holiday
4. **Ngày cưới:** Wedding anniversary
5. **Kỷ niệm:** Other anniversaries
6. **Khác:** Other events

### Event Group (Nhóm sự kiện)
**Definition:** Cách tổ chức events thành các nhóm logic (Gia đình nội, ngoại, bạn bè...)  
**Features:**
- User-created
- One event can belong to multiple groups
- Custom color & icon per group

### Notification / Push Notification (Thông báo)
**Definition:** Alert được gửi đến thiết bị user để nhắc về sự kiện sắp tới.  
**Types:**
- Lock screen notification
- In-app notification
- Badge count

### Notification Schedule (Lịch thông báo)
**Definition:** Kịch bản gửi thông báo dựa trên loại sự kiện.  
**Default schedule:**
- Giỗ: 3 days before, increasing frequency
- Birthday: 1 day before

### Countdown (Đếm ngược)
**Definition:** Số ngày còn lại đến sự kiện.  
**Display:** "Còn X ngày" hoặc "Hôm nay"

### Widget
**Definition:** Component hiển thị trên home screen của điện thoại (iOS & Android).  
**Purpose:** Xem nhanh sự kiện sắp tới mà không cần mở app

### Share Link (Link chia sẻ)
**Definition:** URL đặc biệt cho phép user chia sẻ events với người khác.  
**Format:** `https://lunar-app.link/share/{uniqueId}`  
**Behavior:** Người nhận click → Mở app (nếu có) → Xác nhận import events

### Export to Calendar
**Definition:** Tính năng xuất events từ app sang Google Calendar hoặc lịch thiết bị.  
**Process:** Convert lunar → solar dates, create calendar events with reminders

### Onboarding
**Definition:** Quá trình giới thiệu app cho user lần đầu sử dụng.  
**Steps:** Welcome → Features → Permissions → Optional login

---

## Development Terms

### Sprint
**Definition:** Chu kỳ phát triển ngắn (thường 1-2 tuần) trong Agile methodology.  
**This project:** 7-day sprint (1 tuần) cho MVP

### Epic
**Definition:** Tập hợp lớn các user stories liên quan, thường mất nhiều sprints.  
**Examples:** "Event Management", "Notification System"

### User Story
**Definition:** Mô tả feature từ góc nhìn người dùng.  
**Format:** "As a [user], I want [feature] so that [benefit]"  
**Example:** "As a user, I want to create events with lunar dates so that I can remember death anniversaries"

### Task
**Definition:** Đơn vị công việc nhỏ nhất trong development, thuộc một user story.  
**Example:** "Create EventForm component", "Write unit tests for lunar converter"

### Acceptance Criteria (AC)
**Definition:** Điều kiện cụ thể để xác định một story/task đã hoàn thành.  
**Format:** Checklist of requirements  
**Example:** "Event appears in list sorted by date"

### Pull Request (PR) / Merge Request (MR)
**Definition:** Request để merge code từ feature branch vào develop/main branch.  
**Process:**
1. Developer creates PR
2. PM reviews code
3. PM comments if changes needed
4. Developer fixes
5. PM approves & merges

### CI/CD (Continuous Integration / Continuous Deployment)
**Definition:** Automated process to test, build, và deploy code.  
**This project:** GitHub Actions for automated testing và deployment

### Code Review
**Definition:** Process where PM (hoặc tech lead) kiểm tra code trước khi merge.  
**Purpose:** Ensure code quality, catch bugs, share knowledge

### Branch Strategy
**Definition:** Cách tổ chức Git branches.  
**This project:**
- `main`: Production-ready code
- `develop`: Development branch
- `feature/*`: Feature branches
- `bugfix/*`: Bug fix branches

### Linting
**Definition:** Automated code checking for style và syntax errors.  
**Tools:** ESLint (JavaScript/TypeScript)

### Test Coverage
**Definition:** Percentage of code covered by tests.  
**Target:** > 80%

### API Endpoint
**Definition:** URL on server that app calls to get/send data.  
**Example:** `POST /api/events` to create new event

### Schema / Data Model
**Definition:** Structure of data in database (tables, fields, relationships).  
**Example:** Event table với fields: id, name, date, type, user_id...

### Migration
**Definition:** Scripts to update database schema (add tables, modify fields...).  
**Purpose:** Version control for database changes

### Seed Data
**Definition:** Initial data inserted into database for development/testing.  
**Example:** Sample events, test users

---

## Acronyms

- **API:** Application Programming Interface
- **CRUD:** Create, Read, Update, Delete
- **JWT:** JSON Web Token
- **UI:** User Interface
- **UX:** User Experience
- **PR/MR:** Pull Request / Merge Request
- **CI/CD:** Continuous Integration / Continuous Deployment
- **MVP:** Minimum Viable Product
- **AC:** Acceptance Criteria
- **DB:** Database
- **GMT:** Greenwich Mean Time
- **UTC:** Coordinated Universal Time
- **iOS:** iPhone Operating System (Apple)
- **FCM:** Firebase Cloud Messaging
- **OS:** Operating System

---

## Vietnamese Phrases Used in App

| Vietnamese | English | Context |
|------------|---------|---------|
| Còn X ngày | X days left | Countdown display |
| Hôm nay | Today | When event is today |
| Ngày mai | Tomorrow | When event is tomorrow |
| Thêm sự kiện | Add event | Button text |
| Sửa | Edit | Button text |
| Xóa | Delete | Button text |
| Chia sẻ | Share | Button text |
| Lưu | Save | Button text |
| Hủy | Cancel | Button text |
| Xác nhận | Confirm | Dialog button |
| Đóng | Close | Notification action |
| Tắt hết | Turn off all | Notification action |
| Nhắc lại sau | Remind later | Notification action |
| Cài đặt | Settings | Menu item |
| Đăng nhập | Login | Action |
| Đăng ký | Register | Action |
| Đăng xuất | Logout | Action |

---

**End of Glossary**

This document will be updated as new terms are introduced during development.

