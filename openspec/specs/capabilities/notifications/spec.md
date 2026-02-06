# Notifications Capability Specification

## Overview

Push notification system for reminding users about upcoming events.

---

## Features

### FR-NOTIF-001: Default Notification Schedule
- **Priority:** Critical

**For Giỗ/Chạp (3 days before):**
| Day | Times/Day | Schedule |
|-----|-----------|----------|
| 3 days before | 2 | 6:30, 18:30 |
| 2 days before | 4 | 6:30, 11:45, 18:30, 20:30 |
| 1 day before | 4 | 6:30, 11:45, 18:30, 20:30 |
| Event day | 4 | 6:30, 11:45, 18:30, 20:30 |

**For Other Events (1 day before):**
| Day | Times/Day | Schedule |
|-----|-----------|----------|
| 1 day before | 2 | 6:30, 18:30 |
| Event day | 4 | 6:30, 11:45, 18:30, 20:30 |

### FR-NOTIF-002: Custom Time Slots
- **Priority:** Medium
- User can change 4 default times
- Global setting for all events
- Slots: Morning, Noon, Evening, Night

### FR-NOTIF-003: Custom Frequency
- **Priority:** Low
- Reduce notifications (2 instead of 4)
- Select which slots to receive

### FR-NOTIF-004: Notification Content
- **Title:** "Còn X ngày đến [Tên sự kiện]"
- **Body:** "[Ngày dương] ([Ngày âm lịch])"
- **Action:** Tap → Open event detail

### FR-NOTIF-005: Notification Actions
- **Close:** Dismiss this one, receive next
- **Mute All:** Stop all for this event
- **Snooze:** Remind in 1 hour
- **In-app toggle:** On/off per event

### FR-NOTIF-006: Notification Platform
- **Platform:** OneSignal
- Push to iOS and Android
- Scheduled notifications
- Delivery tracking

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/notifications/upcoming` | List pending notifications |
| POST | `/notifications/test` | Send test notification |
| PUT | `/notifications/settings` | Update global settings |

---

## Data Model

### notifications Table
- `id` UUID (PK)
- `event_id` UUID (FK)
- `user_id` UUID (FK)
- `scheduled_at` TIMESTAMP
- `sent_at` TIMESTAMP
- `onesignal_notification_id` VARCHAR(100)
- `status`: pending, sent, failed, cancelled
- `error_message` TEXT

### devices Table
- `id` UUID (PK)
- `user_id` UUID (FK)
- `device_token` VARCHAR(255)
- `platform`: ios, android
- `onesignal_player_id` VARCHAR(100)
- `is_active` BOOLEAN

See [notifications table](../../../docs/data-model/tables/notifications.md) for full schema.

---

## OneSignal Integration

### Configuration
```typescript
{
  appId: process.env.ONESIGNAL_APP_ID,
  restApiKey: process.env.ONESIGNAL_REST_API_KEY
}
```

### Notification Payload
```typescript
{
  include_player_ids: string[],
  headings: { vi: "Còn 3 ngày đến Giỗ ông nội" },
  contents: { vi: "18/05/2026 (20/4 âm lịch)" },
  data: { event_id: "uuid" },
  ios_badgeType: 'Increase',
  android_channel_id: 'lunar-reminders'
}
```

---

## Scheduling Strategy

### Pre-calculation Approach
1. When event created/updated:
   - Calculate all notification times for next year
   - Store in `notifications` table
2. Cron job runs every hour
3. Find notifications due in next hour
4. Send via OneSignal
5. Update status

---

## User Stories

**US-NOTIF-1:** As a user, I want notifications before events so I can prepare.

**US-NOTIF-2:** As a user, I want to customize notification times to match my schedule.

**US-NOTIF-3:** As a user, I want to mute specific events so I'm not disturbed.

---

## Reference Documentation

- [Architecture Overview](../../../docs/architecture/index.md)
- [Notifications Table (full schema)](../../../docs/data-model/tables/notifications.md)
- [Data Types & Enums](../../../docs/data-model/enums.md) — NotificationStatus, Platform
- [Backend Setup Guide](../../../docs/guides/BACKEND_SETUP.md)

---

## Related Specs

- [Events Capability](../events/spec.md)
- [Settings Capability](../settings/spec.md)
