# Notification System Implementation

## Overview

Push notification system using OneSignal to remind users about upcoming events.

---

## OneSignal Integration

### Setup

```bash
npm install onesignal-node
```

### Configuration

```typescript
// onesignal.config.ts
export const oneSignalConfig = {
  appId: process.env.ONESIGNAL_APP_ID,
  restApiKey: process.env.ONESIGNAL_REST_API_KEY,
  userAuthKey: process.env.ONESIGNAL_USER_AUTH_KEY
};
```

### Provider

```typescript
@Injectable()
export class OneSignalProvider {
  private client: OneSignal.Client;

  constructor() {
    this.client = new OneSignal.Client(
      oneSignalConfig.appId,
      oneSignalConfig.restApiKey
    );
  }

  async send(notification: OneSignalNotification) {
    return this.client.createNotification({
      include_player_ids: notification.playerIds,
      headings: notification.headings,
      contents: notification.contents,
      data: notification.data,
      ios_badgeType: 'Increase',
      ios_badgeCount: 1,
      android_channel_id: 'lunar-reminders'
    });
  }
}
```

---

## Notification Scheduling

### Strategy: Pre-calculation

When an event is created or updated:
1. Calculate all notification times for the next year
2. Store in `notifications` table
3. Cron job checks hourly for pending notifications

### Scheduling Service

```typescript
async scheduleForEvent(event: Event) {
  const notifications = [];
  const currentYear = new Date().getFullYear();

  // Get solar date for this year
  let solarDate = event.calendarType === 'lunar'
    ? await this.lunarService.convertToSolar(event.lunarDate, currentYear)
    : new Date(event.solarDate);

  // Calculate notification times based on event type
  const daysBefore = event.type === 'gio' ? 3 : 1;
  const timeSlots = event.notifyTimeSlots || DEFAULT_TIME_SLOTS;

  for (let i = daysBefore; i >= 0; i--) {
    const notifDate = new Date(solarDate);
    notifDate.setDate(notifDate.getDate() - i);

    // Determine times per day (progressive pattern)
    const timesForDay = i === daysBefore ? 2 : 4;
    const slots = timeSlots.slice(0, timesForDay);

    for (const slot of slots) {
      notifications.push({
        eventId: event.id,
        userId: event.userId,
        scheduledAt: new Date(notifDate.setHours(slot.hour, slot.minute)),
        status: 'pending'
      });
    }
  }

  await this.notificationRepo.save(notifications);
}
```

---

## Notification Scheduler (Cron)

```typescript
@Injectable()
export class NotificationScheduler {
  // Run every hour
  @Cron('0 * * * *')
  async checkUpcomingNotifications() {
    const now = new Date();
    const oneHourLater = new Date(now.getTime() + 60 * 60 * 1000);

    const notifications = await this.notifRepo.find({
      where: {
        scheduledAt: Between(now, oneHourLater),
        status: 'pending'
      },
      relations: ['event', 'user', 'user.devices']
    });

    for (const notif of notifications) {
      await this.sendNotification(notif);
    }
  }

  private async sendNotification(notif: Notification) {
    try {
      const result = await this.onesignal.send({
        playerIds: notif.user.devices.map(d => d.onesignalPlayerId),
        headings: { vi: `Còn ${notif.countdownDays} ngày đến ${notif.event.name}` },
        contents: { vi: `${notif.event.solarDate} (${notif.event.lunarDate})` },
        data: { eventId: notif.event.id }
      });

      notif.status = 'sent';
      notif.sentAt = new Date();
      notif.onesignalNotificationId = result.id;
    } catch (error) {
      notif.status = 'failed';
      notif.errorMessage = error.message;
    }

    await this.notifRepo.save(notif);
  }
}
```

---

## Default Time Slots

```typescript
const DEFAULT_TIME_SLOTS = [
  { hour: 6, minute: 30 },   // Morning
  { hour: 11, minute: 45 },  // Noon
  { hour: 18, minute: 30 },  // Evening
  { hour: 20, minute: 30 }   // Night
];
```

---

## Progressive Notification Pattern

### Giỗ/Chạp (3 days before)

| Day | Times | Schedule |
|-----|-------|----------|
| D-3 | 2 | 6:30, 18:30 |
| D-2 | 4 | 6:30, 11:45, 18:30, 20:30 |
| D-1 | 4 | 6:30, 11:45, 18:30, 20:30 |
| D-0 | 4 | 6:30, 11:45, 18:30, 20:30 |

### Other Events (1 day before)

| Day | Times | Schedule |
|-----|-------|----------|
| D-1 | 2 | 6:30, 18:30 |
| D-0 | 4 | 6:30, 11:45, 18:30, 20:30 |

---

## Frontend Handling

```typescript
// NotificationService.ts
import OneSignal from 'react-native-onesignal';

class NotificationService {
  async initialize() {
    OneSignal.setAppId(ONESIGNAL_APP_ID);

    // Request permissions
    const permission = await OneSignal.promptForPushNotificationsWithUserResponse();

    // Register device
    const deviceState = await OneSignal.getDeviceState();
    await api.registerDevice({
      deviceToken: deviceState.pushToken,
      platform: Platform.OS,
      onesignalPlayerId: deviceState.userId
    });

    // Handle notification opened
    OneSignal.setNotificationOpenedHandler((event) => {
      const eventId = event.notification.additionalData?.eventId;
      if (eventId) {
        navigation.navigate('EventDetail', { id: eventId });
      }
    });
  }
}
```

---

## Related

- [Notifications Capability Spec](../../openspec/specs/capabilities/notifications/spec.md)
- [Notifications API](../api/notifications-endpoints.md)
