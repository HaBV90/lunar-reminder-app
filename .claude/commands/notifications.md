# Notifications Skill

This skill provides patterns for the progressive notification system in the Lunar Reminder App.

## Notification Schedules

### Default Schedules by Event Type

**Giỗ/Chạp (Death Anniversary)**
- Notify 3 days before
- High importance, need time to prepare offerings

```
Day -3: 6:30, 18:30 (2 times)
Day -2: 6:30, 11:45, 18:30, 20:30 (4 times)
Day -1: 6:30, 11:45, 18:30, 20:30 (4 times)
Day 0:  6:30, 11:45, 18:30, 20:30 (4 times)
```

**Sinh nhật/Lễ/Ngày cưới (Birthday/Festival/Wedding)**
- Notify 1 day before
- Moderate importance

```
Day -1: 6:30, 18:30 (2 times)
Day 0:  6:30, 11:45, 18:30, 20:30 (4 times)
```

### Default Time Slots

```typescript
export const DEFAULT_TIME_SLOTS = [
  { hour: 6, minute: 30 },   // Morning - wake up reminder
  { hour: 11, minute: 45 },  // Lunch time
  { hour: 18, minute: 30 },  // After work
  { hour: 20, minute: 30 },  // Evening
];
```

## Notification Service

```typescript
// modules/notifications/notifications.service.ts
import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository, LessThanOrEqual, MoreThan, In } from 'typeorm';
import { Notification } from './entities/notification.entity';
import { Event } from '@modules/events/entities/event.entity';
import { OneSignalProvider } from './providers/onesignal.provider';
import { LunarCalendarService } from '@modules/lunar-calendar/lunar-calendar.service';
import { NotificationStatus, EventType } from '@common/enums';

const DEFAULT_TIME_SLOTS = [
  { hour: 6, minute: 30 },
  { hour: 11, minute: 45 },
  { hour: 18, minute: 30 },
  { hour: 20, minute: 30 },
];

@Injectable()
export class NotificationsService {
  constructor(
    @InjectRepository(Notification)
    private readonly notificationRepository: Repository<Notification>,
    private readonly oneSignalProvider: OneSignalProvider,
    private readonly lunarCalendarService: LunarCalendarService,
  ) {}

  /**
   * Schedule all notifications for an event
   */
  async scheduleForEvent(event: Event): Promise<void> {
    if (!event.notifyEnabled) return;

    const notifications: Partial<Notification>[] = [];
    const currentYear = new Date().getFullYear();

    // Get solar date for current year
    const solarDate = await this.getSolarDateForYear(event, currentYear);

    // Calculate notification schedule based on event type
    const schedule = this.getNotificationSchedule(event);

    for (const scheduleItem of schedule) {
      const { daysBefore, timeSlots } = scheduleItem;

      for (const slot of timeSlots) {
        const scheduledAt = new Date(solarDate);
        scheduledAt.setDate(scheduledAt.getDate() - daysBefore);
        scheduledAt.setHours(slot.hour, slot.minute, 0, 0);

        // Only schedule future notifications
        if (scheduledAt > new Date()) {
          notifications.push({
            eventId: event.id,
            userId: event.userId,
            scheduledAt,
            status: NotificationStatus.PENDING,
          });
        }
      }
    }

    if (notifications.length > 0) {
      await this.notificationRepository.save(notifications);
    }
  }

  /**
   * Get notification schedule based on event type
   */
  private getNotificationSchedule(event: Event): ScheduleItem[] {
    const timeSlots = event.notifyTimeSlots || DEFAULT_TIME_SLOTS;
    const daysBefore = event.notifyDaysBefore ?? this.getDefaultDaysBefore(event.type);
    const timesPerDay = event.notifyTimesPerDay || 4;

    const schedule: ScheduleItem[] = [];

    for (let day = daysBefore; day >= 0; day--) {
      let slotsForDay: TimeSlot[];

      if (day === daysBefore) {
        // First reminder day: 2 times
        slotsForDay = [timeSlots[0], timeSlots[2]].filter(Boolean);
      } else {
        // Other days: up to 4 times based on settings
        slotsForDay = timeSlots.slice(0, timesPerDay);
      }

      schedule.push({ daysBefore: day, timeSlots: slotsForDay });
    }

    return schedule;
  }

  /**
   * Get default days before based on event type
   */
  private getDefaultDaysBefore(type: EventType): number {
    switch (type) {
      case EventType.GIO:
        return 3; // Death anniversary needs more prep time
      case EventType.SINH_NHAT:
      case EventType.LE:
      case EventType.NGAY_CUOI:
        return 1;
      default:
        return 1;
    }
  }

  /**
   * Cancel all pending notifications for an event
   */
  async cancelForEvent(eventId: string): Promise<void> {
    await this.notificationRepository.update(
      { eventId, status: NotificationStatus.PENDING },
      { status: NotificationStatus.CANCELLED }
    );
  }

  /**
   * Get solar date for an event in a specific year
   */
  private async getSolarDateForYear(event: Event, year: number): Promise<Date> {
    if (event.calendarType === 'lunar') {
      return this.lunarCalendarService.convertToSolar({
        day: event.lunarDay,
        month: event.lunarMonth,
        year,
        isLeap: event.lunarIsLeap,
      });
    }

    // For solar dates, adjust year
    const date = new Date(event.solarDate);
    date.setFullYear(year);
    return date;
  }

  /**
   * Get upcoming notifications for a user
   */
  async getUpcoming(userId: string, limit = 10): Promise<Notification[]> {
    return this.notificationRepository.find({
      where: {
        userId,
        status: NotificationStatus.PENDING,
        scheduledAt: MoreThan(new Date()),
      },
      relations: ['event'],
      order: { scheduledAt: 'ASC' },
      take: limit,
    });
  }

  /**
   * Update user notification settings
   */
  async updateSettings(userId: string, settings: NotificationSettings): Promise<void> {
    // Update user preferences in user table
    // Reschedule all pending notifications with new settings

    if (settings.timeSlots) {
      // Cancel and reschedule all pending notifications
      await this.notificationRepository.update(
        { userId, status: NotificationStatus.PENDING },
        { status: NotificationStatus.CANCELLED }
      );

      // Get all active events for user
      // Reschedule with new time slots
    }
  }

  /**
   * Send a test notification to a user
   */
  async sendTest(userId: string, eventId: string): Promise<void> {
    const notification = await this.notificationRepository.findOne({
      where: { eventId, userId, status: NotificationStatus.PENDING },
      relations: ['event', 'user', 'user.devices'],
    });

    if (notification) {
      await this.sendNotification(notification);
    }
  }
}

interface ScheduleItem {
  daysBefore: number;
  timeSlots: TimeSlot[];
}

interface TimeSlot {
  hour: number;
  minute: number;
}

interface NotificationSettings {
  globalEnabled?: boolean;
  timeSlots?: TimeSlot[];
}
```

## Notification Scheduler (Cron Job)

```typescript
// modules/notifications/schedulers/notification.scheduler.ts
import { Injectable, Logger } from '@nestjs/common';
import { Cron, CronExpression } from '@nestjs/schedule';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository, Between } from 'typeorm';
import { Notification } from '../entities/notification.entity';
import { OneSignalProvider } from '../providers/onesignal.provider';
import { NotificationStatus } from '@common/enums';

@Injectable()
export class NotificationScheduler {
  private readonly logger = new Logger(NotificationScheduler.name);

  constructor(
    @InjectRepository(Notification)
    private readonly notificationRepository: Repository<Notification>,
    private readonly oneSignalProvider: OneSignalProvider,
  ) {}

  /**
   * Run every minute to check for notifications to send
   */
  @Cron(CronExpression.EVERY_MINUTE)
  async handleNotifications() {
    const now = new Date();
    const oneMinuteLater = new Date(now.getTime() + 60 * 1000);

    // Find notifications scheduled for the next minute
    const notifications = await this.notificationRepository.find({
      where: {
        scheduledAt: Between(now, oneMinuteLater),
        status: NotificationStatus.PENDING,
      },
      relations: ['event', 'user', 'user.devices'],
    });

    this.logger.log(`Found ${notifications.length} notifications to send`);

    for (const notification of notifications) {
      await this.sendNotification(notification);
    }
  }

  /**
   * Run daily to schedule notifications for upcoming events
   */
  @Cron(CronExpression.EVERY_DAY_AT_MIDNIGHT)
  async scheduleUpcomingNotifications() {
    this.logger.log('Scheduling notifications for upcoming events');

    // Find events in the next 30 days that need notifications
    // This handles recurring events and year transitions
  }

  /**
   * Run yearly to recalculate lunar dates for the new year
   */
  @Cron('0 0 1 1 *') // January 1st at midnight
  async recalculateLunarDates() {
    this.logger.log('Recalculating lunar dates for new year');

    // Recalculate solar dates for all lunar events
    // Reschedule notifications with new dates
  }

  /**
   * Send a single notification via OneSignal
   */
  private async sendNotification(notification: Notification): Promise<void> {
    try {
      const { event, user } = notification;
      const playerIds = user.devices
        ?.filter(d => d.isActive && d.onesignalPlayerId)
        .map(d => d.onesignalPlayerId);

      if (!playerIds?.length) {
        this.logger.warn(`No active devices for user ${user.id}`);
        notification.status = NotificationStatus.FAILED;
        notification.errorMessage = 'No active devices';
        await this.notificationRepository.save(notification);
        return;
      }

      const countdownDays = this.calculateCountdown(event.solarDate);
      const content = this.buildNotificationContent(event, countdownDays);

      const result = await this.oneSignalProvider.send({
        playerIds,
        headings: content.title,
        contents: content.body,
        data: {
          eventId: event.id,
          type: 'event_reminder',
        },
      });

      notification.status = NotificationStatus.SENT;
      notification.sentAt = new Date();
      notification.onesignalNotificationId = result.id;
      await this.notificationRepository.save(notification);

      this.logger.log(`Sent notification for event ${event.id}`);
    } catch (error) {
      this.logger.error(`Failed to send notification: ${error.message}`);

      notification.status = NotificationStatus.FAILED;
      notification.errorMessage = error.message;
      await this.notificationRepository.save(notification);
    }
  }

  /**
   * Calculate countdown days from today
   */
  private calculateCountdown(solarDate: Date): number {
    const today = new Date();
    today.setHours(0, 0, 0, 0);

    const eventDate = new Date(solarDate);
    eventDate.setHours(0, 0, 0, 0);

    return Math.ceil((eventDate.getTime() - today.getTime()) / (1000 * 60 * 60 * 24));
  }

  /**
   * Build notification content
   */
  private buildNotificationContent(
    event: Event,
    countdownDays: number
  ): { title: Record<string, string>; body: Record<string, string> } {
    const lunarDateStr = event.calendarType === 'lunar'
      ? `${event.lunarDay}/${event.lunarMonth} âm lịch`
      : '';

    const solarDateStr = new Date(event.solarDate).toLocaleDateString('vi-VN');

    let titleVi: string;
    let titleEn: string;

    if (countdownDays === 0) {
      titleVi = `Hôm nay là ${event.name}`;
      titleEn = `Today is ${event.name}`;
    } else if (countdownDays === 1) {
      titleVi = `Ngày mai là ${event.name}`;
      titleEn = `Tomorrow is ${event.name}`;
    } else {
      titleVi = `Còn ${countdownDays} ngày đến ${event.name}`;
      titleEn = `${countdownDays} days until ${event.name}`;
    }

    const bodyVi = lunarDateStr
      ? `${solarDateStr} (${lunarDateStr})`
      : solarDateStr;

    const bodyEn = lunarDateStr
      ? `${solarDateStr} (Lunar ${event.lunarMonth}/${event.lunarDay})`
      : solarDateStr;

    return {
      title: { vi: titleVi, en: titleEn },
      body: { vi: bodyVi, en: bodyEn },
    };
  }
}
```

## OneSignal Provider

```typescript
// modules/notifications/providers/onesignal.provider.ts
import { Injectable, Logger } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import * as OneSignal from 'onesignal-node';

interface SendNotificationParams {
  playerIds: string[];
  headings: Record<string, string>;
  contents: Record<string, string>;
  data?: Record<string, any>;
  scheduledAt?: Date;
}

interface NotificationResult {
  id: string;
  recipients: number;
}

@Injectable()
export class OneSignalProvider {
  private readonly logger = new Logger(OneSignalProvider.name);
  private readonly client: OneSignal.Client;

  constructor(private readonly configService: ConfigService) {
    this.client = new OneSignal.Client(
      configService.get('ONESIGNAL_APP_ID'),
      configService.get('ONESIGNAL_REST_API_KEY')
    );
  }

  /**
   * Send notification to specific players
   */
  async send(params: SendNotificationParams): Promise<NotificationResult> {
    const notification: OneSignal.NotificationByDeviceBuilder = {
      include_player_ids: params.playerIds,
      headings: params.headings,
      contents: params.contents,
      data: params.data,
      ios_badgeType: 'Increase',
      ios_badgeCount: 1,
      android_channel_id: 'lunar-reminders',
      priority: 10,
      ttl: 86400, // 24 hours
    };

    if (params.scheduledAt) {
      notification.send_after = params.scheduledAt.toISOString();
    }

    try {
      const response = await this.client.createNotification(notification);

      this.logger.log(`Notification sent: ${response.body.id}`);

      return {
        id: response.body.id,
        recipients: response.body.recipients || 0,
      };
    } catch (error) {
      this.logger.error(`OneSignal error: ${error.message}`);
      throw error;
    }
  }

  /**
   * Cancel a scheduled notification
   */
  async cancel(notificationId: string): Promise<void> {
    try {
      await this.client.cancelNotification(notificationId);
      this.logger.log(`Cancelled notification: ${notificationId}`);
    } catch (error) {
      this.logger.error(`Failed to cancel notification: ${error.message}`);
    }
  }

  /**
   * Register a new device
   */
  async registerDevice(deviceInfo: {
    playerId?: string;
    deviceToken: string;
    platform: 'ios' | 'android';
    userId?: string;
  }): Promise<string> {
    const device = {
      device_type: deviceInfo.platform === 'ios' ? 0 : 1,
      identifier: deviceInfo.deviceToken,
      language: 'vi',
      timezone: 25200, // GMT+7 in seconds
      external_user_id: deviceInfo.userId,
    };

    try {
      const response = await this.client.addDevice(device);
      return response.body.id;
    } catch (error) {
      this.logger.error(`Failed to register device: ${error.message}`);
      throw error;
    }
  }
}
```

## Frontend Notification Handling

```typescript
// services/notificationService.ts
import messaging from '@react-native-firebase/messaging';
import OneSignal from 'react-native-onesignal';
import { Platform } from 'react-native';
import { navigationRef } from '@navigation/RootNavigator';
import { api } from './api';
import { ONESIGNAL_APP_ID } from '@constants/config';

class NotificationService {
  private initialized = false;

  async initialize() {
    if (this.initialized) return;

    // Request permissions
    const authStatus = await messaging().requestPermission();
    const enabled =
      authStatus === messaging.AuthorizationStatus.AUTHORIZED ||
      authStatus === messaging.AuthorizationStatus.PROVISIONAL;

    if (!enabled) {
      console.log('Notification permission denied');
      return;
    }

    // Initialize OneSignal
    OneSignal.setAppId(ONESIGNAL_APP_ID);

    // Handle notification opened
    OneSignal.setNotificationOpenedHandler((openedEvent) => {
      const { notification } = openedEvent;
      const eventId = notification.additionalData?.eventId;

      if (eventId) {
        this.navigateToEvent(eventId);
      }
    });

    // Handle foreground notifications
    OneSignal.setNotificationWillShowInForegroundHandler((notificationReceivedEvent) => {
      const notification = notificationReceivedEvent.getNotification();
      // Show the notification
      notificationReceivedEvent.complete(notification);
    });

    // Get device token and register with backend
    await this.registerDevice();

    this.initialized = true;
  }

  private async registerDevice() {
    try {
      const deviceState = await OneSignal.getDeviceState();
      const fcmToken = await messaging().getToken();

      if (deviceState?.userId) {
        await api.post('/devices/register', {
          onesignalPlayerId: deviceState.userId,
          deviceToken: fcmToken,
          platform: Platform.OS,
          appVersion: '1.0.0', // Get from app config
          osVersion: Platform.Version.toString(),
        });
      }
    } catch (error) {
      console.error('Failed to register device:', error);
    }
  }

  private navigateToEvent(eventId: string) {
    if (navigationRef.isReady()) {
      navigationRef.navigate('EventDetail', { id: eventId });
    }
  }

  async setExternalUserId(userId: string | null) {
    if (userId) {
      OneSignal.setExternalUserId(userId);
    } else {
      OneSignal.removeExternalUserId();
    }
  }

  async setNotificationEnabled(enabled: boolean) {
    OneSignal.disablePush(!enabled);
  }
}

export const notificationService = new NotificationService();
```

## Notification Settings UI

```typescript
// screens/Settings/NotificationSettingsScreen.tsx
import React from 'react';
import { View, Switch, Text } from 'react-native';
import { useNotificationSettings } from '@hooks/useNotificationSettings';
import { TimePicker } from '@components/common/TimePicker';

export const NotificationSettingsScreen: React.FC = () => {
  const {
    settings,
    updateSettings,
    isLoading,
  } = useNotificationSettings();

  const handleToggleGlobal = (enabled: boolean) => {
    updateSettings({ globalEnabled: enabled });
  };

  const handleTimeSlotChange = (index: number, time: { hour: number; minute: number }) => {
    const newSlots = [...settings.timeSlots];
    newSlots[index] = time;
    updateSettings({ timeSlots: newSlots });
  };

  return (
    <View>
      <View style={styles.row}>
        <Text>Bật thông báo</Text>
        <Switch
          value={settings.globalEnabled}
          onValueChange={handleToggleGlobal}
        />
      </View>

      {settings.globalEnabled && (
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>Khung giờ thông báo</Text>

          {['Sáng', 'Trưa', 'Chiều', 'Tối'].map((label, index) => (
            <View key={index} style={styles.row}>
              <Text>{label}</Text>
              <TimePicker
                value={settings.timeSlots[index]}
                onChange={(time) => handleTimeSlotChange(index, time)}
              />
            </View>
          ))}
        </View>
      )}
    </View>
  );
};
```
