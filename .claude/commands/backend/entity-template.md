# Entity Template

## Basic Entity

```typescript
// modules/events/entities/event.entity.ts
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  CreateDateColumn,
  UpdateDateColumn,
  ManyToOne,
  OneToMany,
  JoinColumn,
} from 'typeorm';
import { User } from '@modules/users/entities/user.entity';
import { EventGroup } from './event-group.entity';
import { Notification } from '@modules/notifications/entities/notification.entity';
import { EventType, CalendarType, LeapOption } from '@common/enums';

@Entity('events')
export class Event {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ name: 'user_id' })
  userId: string;

  @ManyToOne(() => User, { onDelete: 'CASCADE' })
  @JoinColumn({ name: 'user_id' })
  user: User;

  @Column({ length: 200 })
  name: string;

  @Column({ type: 'varchar', length: 50 })
  type: EventType;

  @Column({ name: 'calendar_type', type: 'varchar', length: 10 })
  calendarType: CalendarType;

  @Column({ name: 'lunar_day', nullable: true })
  lunarDay: number;

  @Column({ name: 'lunar_month', nullable: true })
  lunarMonth: number;

  @Column({ name: 'lunar_year', nullable: true })
  lunarYear: number;

  @Column({ name: 'lunar_is_leap', default: false })
  lunarIsLeap: boolean;

  @Column({ name: 'lunar_leap_option', type: 'varchar', length: 10, nullable: true })
  lunarLeapOption: LeapOption;

  @Column({ name: 'solar_date', type: 'date' })
  solarDate: Date;

  @Column({ name: 'is_recurring', default: true })
  isRecurring: boolean;

  @Column({ type: 'text', nullable: true })
  notes: string;

  @Column({ name: 'notify_enabled', default: true })
  notifyEnabled: boolean;

  @Column({ name: 'notify_days_before', default: 3 })
  notifyDaysBefore: number;

  @Column({ name: 'notify_time_slots', type: 'json', nullable: true })
  notifyTimeSlots: { hour: number; minute: number }[];

  @Column({ length: 20, nullable: true })
  color: string;

  @Column({ length: 50, nullable: true })
  icon: string;

  @CreateDateColumn({ name: 'created_at' })
  createdAt: Date;

  @UpdateDateColumn({ name: 'updated_at' })
  updatedAt: Date;

  @Column({ name: 'deleted_at', nullable: true })
  deletedAt: Date;

  @OneToMany(() => EventGroup, (eventGroup) => eventGroup.event)
  groups: EventGroup[];

  @OneToMany(() => Notification, (notification) => notification.event)
  notifications: Notification[];
}
```

---

## Junction Table Entity

```typescript
// modules/events/entities/event-group.entity.ts
import { Entity, PrimaryGeneratedColumn, Column, ManyToOne, JoinColumn } from 'typeorm';
import { Event } from './event.entity';
import { Group } from '@modules/groups/entities/group.entity';

@Entity('event_groups')
export class EventGroup {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ name: 'event_id' })
  eventId: string;

  @Column({ name: 'group_id' })
  groupId: string;

  @ManyToOne(() => Event, (event) => event.groups, { onDelete: 'CASCADE' })
  @JoinColumn({ name: 'event_id' })
  event: Event;

  @ManyToOne(() => Group, { onDelete: 'CASCADE' })
  @JoinColumn({ name: 'group_id' })
  group: Group;
}
```

---

## Column Naming

Use `snake_case` in database, `camelCase` in TypeScript:

```typescript
@Column({ name: 'user_id' })
userId: string;
```
