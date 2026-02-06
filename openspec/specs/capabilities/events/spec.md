# Events Capability Specification

## Overview

Core event management capability for creating, editing, and organizing lunar/solar calendar events.

---

## Features

### FR-EVENT-001: Create Event
- **Priority:** Critical
- **Required Fields:**
  - Name (max 100 chars)
  - Type: Giỗ/Kỵ, Sinh nhật, Lễ hội, Ngày cưới, Kỷ niệm, Khác
  - Calendar type: Lunar / Solar
  - Date (via date picker)
- **Optional Fields:**
  - Group assignment
  - Notes (max 500 chars)
  - Notification settings
- **Business Rules:**
  - Giỗ/Kỵ → default lunar calendar, 3 days notice
  - Sinh nhật → default solar calendar, 1 day notice
  - Leap month → option for 1st, 2nd, or both

### FR-EVENT-002: List Events
- Sort by upcoming date
- Display: Name, dates, countdown
- Color-coded by type
- Filter by type, group, search

### FR-EVENT-003: Event Details
- All event information
- Dual date display (solar + lunar)
- Countdown timer
- Actions: Edit, Delete, Share, Mute

### FR-EVENT-004: Edit Event
- Modify all fields
- Confirmation popup
- Recalculate notifications

### FR-EVENT-005: Delete Event
- Soft delete with confirmation
- Preserves shared copies
- Cancels notifications

### FR-EVENT-006: Yearly Recurrence
- Auto-repeat annually
- Auto-convert lunar→solar each year
- Handle leap month per user preference

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/events` | List user's events |
| GET | `/events/:id` | Get event details |
| POST | `/events` | Create event |
| PUT | `/events/:id` | Update event |
| DELETE | `/events/:id` | Delete event |
| POST | `/events/bulk` | Bulk create |

---

## Data Model

### events Table
- `id` UUID (PK)
- `user_id` UUID (FK)
- `name` VARCHAR(200)
- `type` EventType enum
- `calendar_type` CalendarType enum
- `lunar_day`, `lunar_month`, `lunar_year`
- `lunar_is_leap`, `lunar_leap_option`
- `solar_date` DATE
- `notify_enabled`, `notify_days_before`
- `notes`, `location`, `color`, `icon`

See [events table](../../../docs/data-model/tables/events.md) for full schema.

---

## Event Types

| Type | Vietnamese | Default Calendar | Default Notice |
|------|------------|------------------|----------------|
| `gio` | Giỗ/Kỵ | Lunar | 3 days |
| `sinh_nhat` | Sinh nhật | Solar | 1 day |
| `le` | Lễ hội | Lunar | 1 day |
| `ngay_cuoi` | Ngày cưới | Solar | 1 day |
| `ky_niem` | Kỷ niệm | Solar | 1 day |
| `khac` | Khác | Solar | 1 day |

---

## Event Colors

| Type | Color |
|------|-------|
| Giỗ/Kỵ | Purple (#5E35B1) / Navy (#283593) |
| Lễ hội | Red (#D32F2F) + Gold (#FBC02D) |
| Sinh nhật | Gold (#FBC02D) + Pink (#F48FB1) |
| Ngày cưới | Dark red (#C62828) + Gold (#FFD54F) |
| Khác | Gray (#9E9E9E) |

---

## User Stories

**US-EVENT-1:** As a user, I want to create an event with lunar date so I remember death anniversaries.

**US-EVENT-2:** As a user, I want to edit an event so I can fix mistakes.

**US-EVENT-3:** As a user, I want to delete an event so I can remove outdated info.

---

## Reference Documentation

- [Architecture Overview](../../../docs/architecture/index.md)
- [Events Table (full schema)](../../../docs/data-model/tables/events.md)
- [Data Types & Enums](../../../docs/data-model/enums.md) — EventType, CalendarType
- [UI/UX Design System](../../../docs/ui-ux/index.md)
- [Glossary](../../../docs/reference/glossary.md) — Vietnamese event terms

---

## Related Specs

- [Calendar Capability](../calendar/spec.md)
- [Groups Capability](../groups/spec.md)
- [Notifications Capability](../notifications/spec.md)
