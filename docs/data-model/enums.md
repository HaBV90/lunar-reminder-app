# Data Types and Enums

**Purpose:** TypeScript enum definitions for database field values

---

## Event Types

```typescript
enum EventType {
  GIO = 'gio',                // Gio/Ky (Death anniversary)
  SINH_NHAT = 'sinh_nhat',    // Sinh nhat (Birthday)
  LE = 'le',                  // Le hoi (Festival)
  NGAY_CUOI = 'ngay_cuoi',    // Ngay cuoi (Wedding anniversary)
  KY_NIEM = 'ky_niem',        // Ky niem (Other anniversary)
  KHAC = 'khac'               // Khac (Other)
}
```

| Value | Vietnamese | English |
|-------|------------|---------|
| `gio` | Gio/Ky | Death anniversary |
| `sinh_nhat` | Sinh nhat | Birthday |
| `le` | Le hoi | Festival |
| `ngay_cuoi` | Ngay cuoi | Wedding anniversary |
| `ky_niem` | Ky niem | Other anniversary |
| `khac` | Khac | Other |

---

## Calendar Types

```typescript
enum CalendarType {
  LUNAR = 'lunar',  // Lich am
  SOLAR = 'solar'   // Lich duong
}
```

| Value | Vietnamese | English |
|-------|------------|---------|
| `lunar` | Lich am | Lunar calendar |
| `solar` | Lich duong | Solar/Gregorian calendar |

---

## Notification Status

```typescript
enum NotificationStatus {
  PENDING = 'pending',
  SENT = 'sent',
  FAILED = 'failed',
  CANCELLED = 'cancelled'
}
```

| Value | Description |
|-------|-------------|
| `pending` | Not sent yet, waiting for scheduled time |
| `sent` | Successfully delivered to device |
| `failed` | Error occurred during sending |
| `cancelled` | User disabled notifications |

---

## Share Types

```typescript
enum ShareType {
  EVENT = 'event',  // Single event
  GROUP = 'group',  // Event group
  ALL = 'all'       // All user's events
}
```

| Value | Description |
|-------|-------------|
| `event` | Share a single event |
| `group` | Share all events in a group |
| `all` | Share all user's events |

---

## Platform Types

```typescript
enum Platform {
  IOS = 'ios',
  ANDROID = 'android'
}
```

| Value | Description |
|-------|-------------|
| `ios` | Apple iOS devices |
| `android` | Android devices |

---

## Sync Types

```typescript
enum SyncType {
  PULL = 'pull',
  PUSH = 'push'
}
```

| Value | Description |
|-------|-------------|
| `pull` | Client pulls data from server |
| `push` | Client pushes data to server |

---

## Sync Status

```typescript
enum SyncStatus {
  SUCCESS = 'success',
  FAILED = 'failed',
  PARTIAL = 'partial'
}
```

| Value | Description |
|-------|-------------|
| `success` | All items synced successfully |
| `failed` | Sync failed completely |
| `partial` | Some items synced, some failed |

---

## Lunar Leap Option

```typescript
enum LunarLeapOption {
  FIRST = 'first',   // Use the first occurrence of the month
  SECOND = 'second', // Use the second occurrence (leap month)
  BOTH = 'both'      // Notify on both occurrences
}
```

| Value | Description |
|-------|-------------|
| `first` | Use the first occurrence of the month |
| `second` | Use the second occurrence (leap month) |
| `both` | Notify on both occurrences |

---

## Usage in TypeORM Entities

### Example: Event Entity

```typescript
import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

export enum EventType {
  GIO = 'gio',
  SINH_NHAT = 'sinh_nhat',
  LE = 'le',
  NGAY_CUOI = 'ngay_cuoi',
  KY_NIEM = 'ky_niem',
  KHAC = 'khac'
}

export enum CalendarType {
  LUNAR = 'lunar',
  SOLAR = 'solar'
}

@Entity('events')
export class Event {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({
    type: 'varchar',
    length: 50,
    enum: EventType
  })
  type: EventType;

  @Column({
    type: 'varchar',
    length: 10,
    enum: CalendarType
  })
  calendarType: CalendarType;

  // ... other columns
}
```

### Example: Notification Entity

```typescript
import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

export enum NotificationStatus {
  PENDING = 'pending',
  SENT = 'sent',
  FAILED = 'failed',
  CANCELLED = 'cancelled'
}

@Entity('notifications')
export class Notification {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({
    type: 'varchar',
    length: 20,
    enum: NotificationStatus,
    default: NotificationStatus.PENDING
  })
  status: NotificationStatus;

  // ... other columns
}
```

---

## Related Documents

- [Data Model Overview](./index.md)
- [Events Table](./tables/events.md) - Uses EventType, CalendarType
- [Notifications Table](./tables/notifications.md) - Uses NotificationStatus, Platform
- [Shares Table](./tables/shares.md) - Uses ShareType
- [Glossary](../reference/glossary.md) - Vietnamese terms and definitions

---

**End of Data Types and Enums Documentation**
