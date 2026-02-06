# Backend Architecture

**Related Documentation:**
- [System Overview](./system-overview.md)
- [Tech Stack](./tech-stack.md)
- [Frontend Architecture](./frontend-architecture.md)
- [API Index](../api/index.md)
- [Notification System](../features/notification-system.md)

---

## NestJS Folder Structure

```
backend/
├── src/
│   ├── main.ts                    # Entry point
│   ├── app.module.ts              # Root module
│   ├── config/                    # Configuration
│   │   ├── database.config.ts
│   │   ├── jwt.config.ts
│   │   └── onesignal.config.ts
│   ├── modules/
│   │   ├── auth/                  # Authentication
│   │   │   ├── auth.module.ts
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── strategies/
│   │   │   │   ├── jwt.strategy.ts
│   │   │   │   └── google.strategy.ts
│   │   │   └── guards/
│   │   │       └── jwt-auth.guard.ts
│   │   ├── users/                 # User management
│   │   │   ├── users.module.ts
│   │   │   ├── users.controller.ts
│   │   │   ├── users.service.ts
│   │   │   ├── entities/
│   │   │   │   └── user.entity.ts
│   │   │   └── dto/
│   │   │       ├── create-user.dto.ts
│   │   │       └── update-user.dto.ts
│   │   ├── events/                # Event management
│   │   │   ├── events.module.ts
│   │   │   ├── events.controller.ts
│   │   │   ├── events.service.ts
│   │   │   ├── entities/
│   │   │   │   └── event.entity.ts
│   │   │   └── dto/
│   │   ├── groups/                # Group management
│   │   ├── notifications/         # Notification system
│   │   │   ├── notifications.module.ts
│   │   │   ├── notifications.service.ts
│   │   │   ├── schedulers/
│   │   │   │   └── notification.scheduler.ts
│   │   │   └── providers/
│   │   │       └── onesignal.provider.ts
│   │   ├── lunar-calendar/        # Lunar calendar
│   │   │   ├── lunar-calendar.module.ts
│   │   │   ├── lunar-calendar.service.ts
│   │   │   └── algorithms/
│   │   │       └── vietnamese-lunar.ts
│   │   ├── shares/                # Sharing
│   │   ├── sync/                  # Data sync
│   │   └── calendar-export/       # Calendar export
│   ├── common/                    # Shared utilities
│   │   ├── decorators/
│   │   ├── filters/
│   │   ├── interceptors/
│   │   ├── pipes/
│   │   └── utils/
│   └── database/                  # Database
│       ├── migrations/
│       └── seeds/
├── test/                          # E2E tests
├── package.json
├── tsconfig.json
└── README.md
```

---

## Module Design

Each module follows this structure:

- **Module:** Defines dependencies
- **Controller:** Handles HTTP requests
- **Service:** Business logic
- **Repository:** Database access (via TypeORM)
- **DTO:** Data Transfer Objects
- **Entity:** Database models

### Example: Events Module

```typescript
// events.module.ts
@Module({
  imports: [
    TypeOrmModule.forFeature([Event, EventGroup]),
    LunarCalendarModule,
    NotificationsModule
  ],
  controllers: [EventsController],
  providers: [EventsService],
  exports: [EventsService]
})
export class EventsModule {}

// events.controller.ts
@Controller('events')
@UseGuards(JwtAuthGuard)
export class EventsController {
  constructor(private eventsService: EventsService) {}

  @Get()
  findAll(@Query() query: FindEventsDto) {
    return this.eventsService.findAll(query);
  }

  @Post()
  create(@Body() dto: CreateEventDto, @User() user: UserEntity) {
    return this.eventsService.create(dto, user.id);
  }
}

// events.service.ts
@Injectable()
export class EventsService {
  constructor(
    @InjectRepository(Event) private eventRepo: Repository<Event>,
    private lunarCalendarService: LunarCalendarService,
    private notificationsService: NotificationsService
  ) {}

  async create(dto: CreateEventDto, userId: string) {
    // Convert lunar to solar if needed
    if (dto.calendar_type === 'lunar') {
      dto.solar_date = await this.lunarCalendarService.convertToSolar(
        dto.lunar_date
      );
    }

    // Create event
    const event = this.eventRepo.create({ ...dto, user_id: userId });
    await this.eventRepo.save(event);

    // Schedule notifications
    await this.notificationsService.scheduleForEvent(event);

    return event;
  }
}
```

---

## Scheduled Jobs

### Notification Scheduler

```typescript
@Injectable()
export class NotificationScheduler {
  constructor(
    @InjectRepository(Event) private eventRepo: Repository<Event>,
    @InjectRepository(Notification) private notifRepo: Repository<Notification>,
    private onesignalProvider: OneSignalProvider
  ) {}

  // Run every hour
  @Cron('0 * * * *')
  async checkUpcomingNotifications() {
    const now = new Date();
    const oneHourLater = new Date(now.getTime() + 60 * 60 * 1000);

    // Find notifications to send in next hour
    const notifications = await this.notifRepo.find({
      where: {
        scheduled_at: Between(now, oneHourLater),
        status: 'pending'
      },
      relations: ['event', 'user', 'user.devices']
    });

    // Send each notification
    for (const notif of notifications) {
      await this.sendNotification(notif);
    }
  }

  private async sendNotification(notif: Notification) {
    try {
      const result = await this.onesignalProvider.send({
        player_ids: notif.user.devices.map(d => d.onesignal_player_id),
        headings: { vi: `Còn ${notif.countdown_days} ngày đến ${notif.event.name}` },
        contents: { vi: `${notif.event.solar_date} (${notif.event.lunar_date})` },
        data: { event_id: notif.event.id }
      });

      notif.status = 'sent';
      notif.sent_at = new Date();
      notif.onesignal_notification_id = result.id;
      await this.notifRepo.save(notif);
    } catch (error) {
      notif.status = 'failed';
      notif.error_message = error.message;
      await this.notifRepo.save(notif);
    }
  }
}
```
