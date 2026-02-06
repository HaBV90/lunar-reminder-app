# Testing Skill

Generate test code following project conventions.

See rules: `.claude/rules/testing.md`

---

## Frontend Unit Test Templates

### Utility Test

```typescript
import { formatLunarDate, calculateCountdown, isLeapMonth } from '../lunarUtils';

describe('lunarUtils', () => {
  describe('formatLunarDate', () => {
    it('should format standard lunar date', () => {
      expect(formatLunarDate(15, 1, 2026, false)).toBe('15/01 Âm lịch');
    });

    it('should indicate leap month', () => {
      expect(formatLunarDate(15, 4, 2026, true)).toContain('nhuận');
    });

    it('should handle single digit day and month', () => {
      expect(formatLunarDate(5, 3, 2026, false)).toBe('05/03 Âm lịch');
    });
  });

  describe('calculateCountdown', () => {
    it('should return positive days for future date', () => {
      const futureDate = new Date();
      futureDate.setDate(futureDate.getDate() + 10);
      expect(calculateCountdown(futureDate)).toBe(10);
    });

    it('should return 0 for today', () => {
      expect(calculateCountdown(new Date())).toBe(0);
    });
  });
});
```

### Hook Test

```typescript
import { renderHook, act, waitFor } from '@testing-library/react-hooks';
import { Provider } from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';
import { useEvents } from '@hooks/useEvents';
import eventsReducer from '@store/slices/eventsSlice';

const createWrapper = (preloadedState = {}) => {
  const store = configureStore({
    reducer: { events: eventsReducer },
    preloadedState,
  });
  return ({ children }: { children: React.ReactNode }) => (
    <Provider store={store}>{children}</Provider>
  );
};

describe('useEvents', () => {
  it('should return empty events initially', () => {
    const { result } = renderHook(() => useEvents(), {
      wrapper: createWrapper(),
    });

    expect(result.current.events).toEqual([]);
    expect(result.current.isLoading).toBe(false);
  });

  it('should return events from preloaded state', () => {
    const mockEvents = [
      { id: '1', name: 'Giỗ ông nội', type: 'gio' },
    ];

    const { result } = renderHook(() => useEvents(), {
      wrapper: createWrapper({
        events: { items: mockEvents, loading: false, error: null },
      }),
    });

    expect(result.current.events).toEqual(mockEvents);
  });

  it('should provide refresh function', async () => {
    const { result } = renderHook(() => useEvents(), {
      wrapper: createWrapper(),
    });

    expect(typeof result.current.refresh).toBe('function');
  });
});
```

### Component Test

```typescript
import React from 'react';
import { render, fireEvent, waitFor } from '@testing-library/react-native';
import { EventCard } from '@components/event/EventCard';
import { createMockEvent } from '@test-utils/factories';

describe('EventCard', () => {
  const mockEvent = createMockEvent({
    name: 'Giỗ ông nội',
    type: 'gio',
  });
  const mockOnPress = jest.fn();

  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('should render event name', () => {
    const { getByText } = render(
      <EventCard event={mockEvent} onPress={mockOnPress} />
    );
    expect(getByText('Giỗ ông nội')).toBeTruthy();
  });

  it('should render event type badge', () => {
    const { getByText } = render(
      <EventCard event={mockEvent} onPress={mockOnPress} />
    );
    expect(getByText('Giỗ')).toBeTruthy();
  });

  it('should call onPress with event id when pressed', () => {
    const { getByText } = render(
      <EventCard event={mockEvent} onPress={mockOnPress} />
    );
    fireEvent.press(getByText('Giỗ ông nội'));
    expect(mockOnPress).toHaveBeenCalledWith(mockEvent.id);
  });

  it('should display countdown days', () => {
    const { getByTestId } = render(
      <EventCard event={mockEvent} onPress={mockOnPress} />
    );
    expect(getByTestId('countdown')).toBeTruthy();
  });

  it('should have accessibility label', () => {
    const { getByLabelText } = render(
      <EventCard event={mockEvent} onPress={mockOnPress} />
    );
    expect(getByLabelText(/Giỗ ông nội/)).toBeTruthy();
  });
});
```

---

## Frontend Integration Test Template

### Screen with Redux + Navigation

```typescript
import React from 'react';
import { waitFor } from '@testing-library/react-native';
import { NavigationContainer } from '@react-navigation/native';
import { renderWithProviders } from '@test-utils/renderWithProviders';
import { EventListScreen } from '@screens/EventListScreen';
import { createMockEvent } from '@test-utils/factories';

// Mock navigation
const mockNavigate = jest.fn();
jest.mock('@react-navigation/native', () => ({
  ...jest.requireActual('@react-navigation/native'),
  useNavigation: () => ({ navigate: mockNavigate }),
}));

describe('EventListScreen', () => {
  const mockEvents = [
    createMockEvent({ id: '1', name: 'Giỗ ông nội', type: 'gio' }),
    createMockEvent({ id: '2', name: 'Sinh nhật mẹ', type: 'sinh_nhat' }),
  ];

  it('should render list of events', async () => {
    const { getByText } = renderWithProviders(
      <NavigationContainer>
        <EventListScreen />
      </NavigationContainer>,
      {
        preloadedState: {
          events: { items: mockEvents, loading: false, error: null },
        },
      },
    );

    await waitFor(() => {
      expect(getByText('Giỗ ông nội')).toBeTruthy();
      expect(getByText('Sinh nhật mẹ')).toBeTruthy();
    });
  });

  it('should show loading spinner when loading', () => {
    const { getByTestId } = renderWithProviders(
      <NavigationContainer>
        <EventListScreen />
      </NavigationContainer>,
      {
        preloadedState: {
          events: { items: [], loading: true, error: null },
        },
      },
    );

    expect(getByTestId('loading-spinner')).toBeTruthy();
  });

  it('should show empty state when no events', () => {
    const { getByText } = renderWithProviders(
      <NavigationContainer>
        <EventListScreen />
      </NavigationContainer>,
      {
        preloadedState: {
          events: { items: [], loading: false, error: null },
        },
      },
    );

    expect(getByText(/chưa có sự kiện/i)).toBeTruthy();
  });

  it('should navigate to event detail on press', async () => {
    const { getByText } = renderWithProviders(
      <NavigationContainer>
        <EventListScreen />
      </NavigationContainer>,
      {
        preloadedState: {
          events: { items: mockEvents, loading: false, error: null },
        },
      },
    );

    fireEvent.press(getByText('Giỗ ông nội'));
    expect(mockNavigate).toHaveBeenCalledWith('EventDetail', { id: '1' });
  });
});
```

---

## Backend Unit Test Templates

### Service Test

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { getRepositoryToken } from '@nestjs/typeorm';
import { NotFoundException, ForbiddenException } from '@nestjs/common';
import { EventsService } from './events.service';
import { Event } from './entities/event.entity';
import { createMockEvent, createMockCreateEventDto } from '@test-utils/factories';

const mockRepository = {
  findOne: jest.fn(),
  find: jest.fn(),
  save: jest.fn(),
  create: jest.fn(),
  softRemove: jest.fn(),
  createQueryBuilder: jest.fn(),
};

describe('EventsService', () => {
  let service: EventsService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        EventsService,
        { provide: getRepositoryToken(Event), useValue: mockRepository },
      ],
    }).compile();

    service = module.get<EventsService>(EventsService);
    jest.clearAllMocks();
  });

  describe('findOne', () => {
    it('should return event when found and owned by user', async () => {
      const event = createMockEvent({ userId: 'user-1' });
      mockRepository.findOne.mockResolvedValue(event);

      const result = await service.findOne('event-1', 'user-1');
      expect(result).toEqual(event);
    });

    it('should throw NotFoundException when not found', async () => {
      mockRepository.findOne.mockResolvedValue(null);

      await expect(service.findOne('event-1', 'user-1'))
        .rejects.toThrow(NotFoundException);
    });
  });

  describe('create', () => {
    it('should create and return event', async () => {
      const dto = createMockCreateEventDto();
      const created = createMockEvent({ ...dto, userId: 'user-1' });
      mockRepository.create.mockReturnValue(created);
      mockRepository.save.mockResolvedValue(created);

      const result = await service.create(dto, 'user-1');
      expect(result.name).toBe(dto.name);
      expect(mockRepository.save).toHaveBeenCalled();
    });
  });

  describe('softDelete', () => {
    it('should soft delete owned event', async () => {
      const event = createMockEvent({ userId: 'user-1' });
      mockRepository.findOne.mockResolvedValue(event);
      mockRepository.softRemove.mockResolvedValue(event);

      await service.softDelete('event-1', 'user-1');
      expect(mockRepository.softRemove).toHaveBeenCalledWith(event);
    });
  });
});
```

### Guard Test

```typescript
import { ExecutionContext, UnauthorizedException } from '@nestjs/common';
import { JwtAuthGuard } from './jwt-auth.guard';

const createMockContext = (headers: Record<string, string> = {}): ExecutionContext => ({
  switchToHttp: () => ({
    getRequest: () => ({ headers }),
    getResponse: () => ({}),
    getNext: () => jest.fn(),
  }),
  getClass: () => Object,
  getHandler: () => jest.fn(),
  getArgs: () => [],
  getArgByIndex: () => null,
  switchToRpc: () => ({} as any),
  switchToWs: () => ({} as any),
  getType: () => 'http' as any,
} as ExecutionContext);

describe('JwtAuthGuard', () => {
  let guard: JwtAuthGuard;

  beforeEach(() => {
    guard = new JwtAuthGuard();
  });

  it('should reject request without authorization header', () => {
    const context = createMockContext();
    expect(() => guard.canActivate(context)).toThrow(UnauthorizedException);
  });

  it('should reject request with invalid token format', () => {
    const context = createMockContext({ authorization: 'Invalid' });
    expect(() => guard.canActivate(context)).toThrow(UnauthorizedException);
  });
});
```

### Pipe Test

```typescript
import { ValidationPipe, BadRequestException } from '@nestjs/common';
import { CreateEventDto } from './dto/create-event.dto';

describe('CreateEventDto validation', () => {
  const pipe = new ValidationPipe({ whitelist: true, forbidNonWhitelisted: true });

  it('should pass with valid data', async () => {
    const dto = { name: 'Giỗ ông nội', type: 'gio', lunarDay: 15, lunarMonth: 1 };
    const result = await pipe.transform(dto, {
      type: 'body',
      metatype: CreateEventDto,
    });
    expect(result.name).toBe('Giỗ ông nội');
  });

  it('should reject empty name', async () => {
    const dto = { name: '', type: 'gio' };
    await expect(
      pipe.transform(dto, { type: 'body', metatype: CreateEventDto }),
    ).rejects.toThrow(BadRequestException);
  });
});
```

---

## Backend Integration Test Template

### Controller with Full Module

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { INestApplication } from '@nestjs/common';
import * as request from 'supertest';
import { getRepositoryToken } from '@nestjs/typeorm';
import { EventsModule } from './events.module';
import { Event } from './entities/event.entity';
import { JwtAuthGuard } from '@common/guards/jwt-auth.guard';
import { createMockEvent } from '@test-utils/factories';

describe('EventsController (integration)', () => {
  let app: INestApplication;
  const mockRepository = {
    find: jest.fn(),
    findOne: jest.fn(),
    save: jest.fn(),
    create: jest.fn(),
  };

  const mockJwtGuard = { canActivate: jest.fn(() => true) };

  beforeAll(async () => {
    const module: TestingModule = await Test.createTestingModule({
      imports: [EventsModule],
    })
      .overrideProvider(getRepositoryToken(Event))
      .useValue(mockRepository)
      .overrideGuard(JwtAuthGuard)
      .useValue(mockJwtGuard)
      .compile();

    app = module.createNestApplication();
    await app.init();
  });

  afterAll(async () => {
    await app.close();
  });

  describe('GET /events', () => {
    it('should return 200 with events array', async () => {
      mockRepository.find.mockResolvedValue([createMockEvent()]);

      const response = await request(app.getHttpServer())
        .get('/events')
        .expect(200);

      expect(response.body).toHaveProperty('data');
      expect(Array.isArray(response.body.data)).toBe(true);
    });
  });

  describe('POST /events', () => {
    it('should create event and return 201', async () => {
      const dto = { name: 'Giỗ bà ngoại', type: 'gio', lunarDay: 20, lunarMonth: 3 };
      const created = createMockEvent(dto);
      mockRepository.create.mockReturnValue(created);
      mockRepository.save.mockResolvedValue(created);

      const response = await request(app.getHttpServer())
        .post('/events')
        .send(dto)
        .expect(201);

      expect(response.body.data.name).toBe('Giỗ bà ngoại');
    });

    it('should return 400 for invalid input', async () => {
      await request(app.getHttpServer())
        .post('/events')
        .send({ name: '' })
        .expect(400);
    });
  });
});
```

---

## Mock Patterns

### Redux Store Mock

```typescript
// test-utils/renderWithProviders.tsx
import React from 'react';
import { render } from '@testing-library/react-native';
import { Provider } from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';
import { rootReducer } from '@store/rootReducer';

export function renderWithProviders(
  ui: React.ReactElement,
  {
    preloadedState = {},
    store = configureStore({ reducer: rootReducer, preloadedState }),
    ...renderOptions
  } = {},
) {
  function Wrapper({ children }: { children: React.ReactNode }) {
    return <Provider store={store}>{children}</Provider>;
  }

  return { store, ...render(ui, { wrapper: Wrapper, ...renderOptions }) };
}
```

### Navigation Mock

```typescript
jest.mock('@react-navigation/native', () => ({
  ...jest.requireActual('@react-navigation/native'),
  useNavigation: () => ({
    navigate: jest.fn(),
    goBack: jest.fn(),
    setOptions: jest.fn(),
  }),
  useRoute: () => ({
    params: { id: 'mock-id' },
  }),
}));
```

### API Service Mock

```typescript
jest.mock('@services/api', () => ({
  api: {
    get: jest.fn(),
    post: jest.fn(),
    put: jest.fn(),
    delete: jest.fn(),
  },
}));
```

### TypeORM Repository Mock

```typescript
export const repositoryMockFactory = () => ({
  find: jest.fn(),
  findOne: jest.fn(),
  save: jest.fn(),
  create: jest.fn(),
  update: jest.fn(),
  delete: jest.fn(),
  softRemove: jest.fn(),
  createQueryBuilder: jest.fn(() => ({
    where: jest.fn().mockReturnThis(),
    andWhere: jest.fn().mockReturnThis(),
    leftJoinAndSelect: jest.fn().mockReturnThis(),
    orderBy: jest.fn().mockReturnThis(),
    skip: jest.fn().mockReturnThis(),
    take: jest.fn().mockReturnThis(),
    getMany: jest.fn().mockResolvedValue([]),
    getOne: jest.fn().mockResolvedValue(null),
    getCount: jest.fn().mockResolvedValue(0),
  })),
});
```

### OneSignal Mock

```typescript
jest.mock('react-native-onesignal', () => ({
  OneSignal: {
    initialize: jest.fn(),
    Notifications: {
      requestPermission: jest.fn().mockResolvedValue(true),
      addEventListener: jest.fn(),
    },
    User: {
      addTag: jest.fn(),
      removeTag: jest.fn(),
    },
  },
}));
```

---

## Test Utilities

### Mock Event Factory

```typescript
// test-utils/factories.ts
import { EventType } from '@types/event';

let idCounter = 0;

export const createMockEvent = (overrides = {}) => ({
  id: `event-${++idCounter}`,
  name: 'Giỗ ông nội',
  type: EventType.GIO,
  userId: 'user-1',
  lunarDay: 15,
  lunarMonth: 1,
  lunarYear: null,
  isLeapMonth: false,
  solarDate: new Date('2026-04-18'),
  notes: null,
  isRecurring: true,
  createdAt: new Date('2026-01-01'),
  updatedAt: new Date('2026-01-01'),
  deletedAt: null,
  ...overrides,
});

export const createMockCreateEventDto = (overrides = {}) => ({
  name: 'Giỗ ông nội',
  type: EventType.GIO,
  lunarDay: 15,
  lunarMonth: 1,
  isLeapMonth: false,
  notes: null,
  ...overrides,
});

export const createMockUser = (overrides = {}) => ({
  id: 'user-1',
  name: 'Nguyễn Văn A',
  email: 'nguyenvana@example.com',
  createdAt: new Date('2026-01-01'),
  ...overrides,
});
```
