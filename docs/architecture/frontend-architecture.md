# Frontend Architecture

**Related Documentation:**
- [System Overview](./system-overview.md)
- [Tech Stack](./tech-stack.md)
- [Backend Architecture](./backend-architecture.md)
- [Data Sync](../features/data-sync.md)

---

## Folder Structure

```
frontend/
├── src/
│   ├── components/           # Reusable UI components
│   │   ├── common/
│   │   │   ├── Button/
│   │   │   ├── Input/
│   │   │   ├── Card/
│   │   │   └── ...
│   │   ├── event/
│   │   │   ├── EventCard/
│   │   │   ├── EventForm/
│   │   │   └── EventList/
│   │   └── calendar/
│   │       ├── LunarCalendar/
│   │       ├── DatePicker/
│   │       └── MonthView/
│   ├── screens/             # Screen components
│   │   ├── Auth/
│   │   │   ├── LoginScreen.tsx
│   │   │   ├── RegisterScreen.tsx
│   │   │   └── OnboardingScreen.tsx
│   │   ├── Home/
│   │   │   ├── HomeScreen.tsx
│   │   │   └── EventDetailScreen.tsx
│   │   ├── Event/
│   │   │   ├── CreateEventScreen.tsx
│   │   │   ├── EditEventScreen.tsx
│   │   │   └── EventListScreen.tsx
│   │   ├── Group/
│   │   │   ├── GroupListScreen.tsx
│   │   │   └── GroupDetailScreen.tsx
│   │   ├── Calendar/
│   │   │   └── CalendarViewScreen.tsx
│   │   └── Settings/
│   │       ├── SettingsScreen.tsx
│   │       ├── NotificationSettingsScreen.tsx
│   │       └── ProfileScreen.tsx
│   ├── navigation/          # Navigation setup
│   │   ├── RootNavigator.tsx
│   │   ├── AuthNavigator.tsx
│   │   ├── MainNavigator.tsx
│   │   └── types.ts
│   ├── store/               # Redux store
│   │   ├── index.ts
│   │   ├── slices/
│   │   │   ├── authSlice.ts
│   │   │   ├── eventSlice.ts
│   │   │   ├── groupSlice.ts
│   │   │   └── notificationSlice.ts
│   │   └── api/            # RTK Query (optional)
│   │       └── apiSlice.ts
│   ├── services/           # API services
│   │   ├── api.ts         # Axios config
│   │   ├── authService.ts
│   │   ├── eventService.ts
│   │   ├── groupService.ts
│   │   └── syncService.ts
│   ├── database/           # Local database
│   │   ├── index.ts
│   │   ├── schema.ts
│   │   ├── migrations/
│   │   └── queries/
│   ├── utils/              # Utilities
│   │   ├── lunarCalendar.ts
│   │   ├── dateHelpers.ts
│   │   ├── validators.ts
│   │   └── storage.ts
│   ├── hooks/              # Custom hooks
│   │   ├── useAuth.ts
│   │   ├── useEvents.ts
│   │   ├── useLunarCalendar.ts
│   │   └── useSync.ts
│   ├── constants/          # Constants
│   │   ├── colors.ts
│   │   ├── eventTypes.ts
│   │   └── config.ts
│   ├── types/              # TypeScript types
│   │   ├── event.ts
│   │   ├── user.ts
│   │   └── api.ts
│   ├── assets/             # Static assets
│   │   ├── images/
│   │   ├── icons/
│   │   └── fonts/
│   ├── i18n/               # Internationalization
│   │   ├── index.ts
│   │   ├── en.json
│   │   └── vi.json
│   └── App.tsx             # Root component
├── __tests__/              # Tests
├── android/                # Android native
├── ios/                    # iOS native
├── package.json
├── tsconfig.json
└── README.md
```

---

## State Management

### Redux Store Structure

```typescript
{
  auth: {
    user: User | null,
    token: string | null,
    isAuthenticated: boolean,
    isLoading: boolean
  },
  events: {
    items: Event[],
    selectedEvent: Event | null,
    filters: EventFilters,
    isLoading: boolean,
    error: string | null
  },
  groups: {
    items: Group[],
    isLoading: boolean
  },
  notifications: {
    settings: NotificationSettings,
    upcoming: Notification[]
  },
  sync: {
    lastSyncTime: string | null,
    isSyncing: boolean,
    pendingChanges: number
  },
  ui: {
    theme: 'light' | 'dark',
    language: 'vi' | 'en'
  }
}
```

---

## Offline Strategy

### Approach: Offline-First

#### 1. Read Operations

- Always read from local SQLite first
- Display data immediately (fast)
- Background sync from server
- Update UI if server has newer data

#### 2. Write Operations

- Save to local SQLite immediately
- Mark as "pending_upload"
- Queue sync task
- Upload to server when online
- Update local with server response

#### 3. Conflict Resolution

- Last Write Wins (simple approach)
- Server timestamp is authoritative
- User can manually resolve if needed (future)

### Sync Queue

```typescript
interface SyncQueueItem {
  id: string;
  operation: 'create' | 'update' | 'delete';
  entity_type: 'event' | 'group';
  entity_id: string;
  data: any;
  timestamp: number;
  retry_count: number;
}
```

---

## Component Architecture

### Container/Presenter Pattern

#### Container (Smart Component)

- Connects to Redux store
- Handles business logic
- Makes API calls
- Manages local state

#### Presenter (Dumb Component)

- Pure UI component
- Receives data via props
- Emits events via callbacks
- No business logic

### Example

```typescript
// Container
function EventListContainer() {
  const events = useSelector(selectEvents);
  const dispatch = useDispatch();

  const handleDelete = (id: string) => {
    dispatch(deleteEvent(id));
  };

  return <EventListPresenter events={events} onDelete={handleDelete} />;
}

// Presenter
function EventListPresenter({ events, onDelete }) {
  return (
    <FlatList
      data={events}
      renderItem={({ item }) => (
        <EventCard event={item} onDelete={() => onDelete(item.id)} />
      )}
    />
  );
}
```
