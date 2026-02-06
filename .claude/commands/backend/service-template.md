# Service Template

## Basic Service

```typescript
// modules/events/events.service.ts
import { Injectable, NotFoundException } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { Event } from './entities/event.entity';
import { EventGroup } from './entities/event-group.entity';
import { CreateEventDto } from './dto/create-event.dto';
import { UpdateEventDto } from './dto/update-event.dto';
import { FindEventsDto } from './dto/find-events.dto';
import { LunarCalendarService } from '@modules/lunar-calendar/lunar-calendar.service';
import { NotificationsService } from '@modules/notifications/notifications.service';
import { CalendarType } from '@common/enums';

@Injectable()
export class EventsService {
  constructor(
    @InjectRepository(Event)
    private readonly eventRepository: Repository<Event>,
    @InjectRepository(EventGroup)
    private readonly eventGroupRepository: Repository<EventGroup>,
    private readonly lunarCalendarService: LunarCalendarService,
    private readonly notificationsService: NotificationsService,
  ) {}

  async findAll(userId: string, query: FindEventsDto) {
    const { page = 1, limit = 20, type, groupId, search } = query;

    const queryBuilder = this.eventRepository
      .createQueryBuilder('event')
      .leftJoinAndSelect('event.groups', 'eventGroup')
      .leftJoinAndSelect('eventGroup.group', 'group')
      .where('event.userId = :userId', { userId })
      .andWhere('event.deletedAt IS NULL');

    if (type) {
      queryBuilder.andWhere('event.type = :type', { type });
    }

    if (groupId) {
      queryBuilder.andWhere('eventGroup.groupId = :groupId', { groupId });
    }

    if (search) {
      queryBuilder.andWhere('event.name ILIKE :search', { search: `%${search}%` });
    }

    queryBuilder
      .orderBy('event.solarDate', 'ASC')
      .skip((page - 1) * limit)
      .take(limit);

    const [events, total] = await queryBuilder.getManyAndCount();

    return {
      events: events.map(this.transformEvent),
      pagination: { page, limit, total, totalPages: Math.ceil(total / limit) },
    };
  }

  async findOne(id: string, userId: string): Promise<Event> {
    const event = await this.eventRepository.findOne({
      where: { id, userId, deletedAt: null },
      relations: ['groups', 'groups.group'],
    });

    if (!event) {
      throw new NotFoundException('Event not found');
    }

    return this.transformEvent(event);
  }

  async create(dto: CreateEventDto, userId: string): Promise<Event> {
    // Convert lunar to solar if needed
    let solarDate = dto.solarDate;
    if (dto.calendarType === CalendarType.LUNAR) {
      solarDate = await this.lunarCalendarService.convertToSolar({
        day: dto.lunarDay,
        month: dto.lunarMonth,
        year: new Date().getFullYear(),
        isLeap: dto.lunarIsLeap,
      });
    }

    const event = this.eventRepository.create({
      ...dto,
      userId,
      solarDate,
    });

    const savedEvent = await this.eventRepository.save(event);

    // Create group associations
    if (dto.groupIds?.length) {
      const eventGroups = dto.groupIds.map((groupId) => ({
        eventId: savedEvent.id,
        groupId,
      }));
      await this.eventGroupRepository.save(eventGroups);
    }

    // Schedule notifications
    if (savedEvent.notifyEnabled) {
      await this.notificationsService.scheduleForEvent(savedEvent);
    }

    return this.findOne(savedEvent.id, userId);
  }

  async update(id: string, dto: UpdateEventDto, userId: string): Promise<Event> {
    const event = await this.eventRepository.findOne({
      where: { id, userId, deletedAt: null },
    });

    if (!event) {
      throw new NotFoundException('Event not found');
    }

    Object.assign(event, dto);
    event.updatedAt = new Date();

    await this.eventRepository.save(event);

    // Reschedule notifications
    await this.notificationsService.cancelForEvent(id);
    if (event.notifyEnabled) {
      await this.notificationsService.scheduleForEvent(event);
    }

    return this.findOne(id, userId);
  }

  async remove(id: string, userId: string): Promise<void> {
    const event = await this.eventRepository.findOne({
      where: { id, userId, deletedAt: null },
    });

    if (!event) {
      throw new NotFoundException('Event not found');
    }

    // Soft delete
    event.deletedAt = new Date();
    await this.eventRepository.save(event);

    // Cancel notifications
    await this.notificationsService.cancelForEvent(id);
  }

  private transformEvent(event: Event) {
    const today = new Date();
    const solarDate = new Date(event.solarDate);
    const countdownDays = Math.ceil(
      (solarDate.getTime() - today.getTime()) / (1000 * 60 * 60 * 24)
    );

    return {
      ...event,
      countdownDays,
      groups: event.groups?.map((eg) => eg.group) || [],
    };
  }
}
```

---

## Key Patterns

1. **Repository injection** - Use `@InjectRepository(Entity)`
2. **Query builder** - For complex queries with joins
3. **Soft delete** - Set `deletedAt` instead of deleting
4. **Transform output** - Add computed fields like countdown
