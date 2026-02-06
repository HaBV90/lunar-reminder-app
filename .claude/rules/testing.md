# Testing Rules

## General Principles

1. **Test behavior, not implementation** - Test what the code does, not how it does it
2. **Tests must be independent** - No shared mutable state between tests
3. **Tests must be deterministic** - Same input = same result, no flaky tests
4. **Arrange-Act-Assert** pattern in every test
5. **One assertion concept per test** - Test one behavior per `it()` block
6. **Descriptive test names** - `it('should throw NotFoundException when event does not exist')`

---

## File Naming & Location

### Frontend

- Unit tests: `__tests__/` directory adjacent to source
- Component tests: `Component.test.tsx` colocated with component
- Hook tests: `useHook.test.ts` in `__tests__/`
- Integration tests: `__tests__/integration/`

### Backend

- Unit tests: colocated `*.spec.ts` (e.g., `events.service.spec.ts`)
- Integration tests: colocated `*.spec.ts` with TestingModule
- E2E tests: `test/` directory at backend root (`*.e2e-spec.ts`)

---

## Coverage Thresholds

| Category | Target | Minimum |
|----------|--------|---------|
| Utilities / Helpers | 90% | 85% |
| Custom Hooks | 80% | 70% |
| Components | 70% | 60% |
| Services (BE) | 80% | 75% |
| Controllers (BE) | 70% | 60% |
| **Overall** | **75%** | **70%** |

Configure in `jest.config.ts`:

```typescript
coverageThreshold: {
  global: {
    branches: 70,
    functions: 75,
    lines: 75,
    statements: 75,
  },
},
```

---

## Frontend Test Patterns

### Utility Tests

```typescript
describe('formatLunarDate', () => {
  it('should format standard lunar date', () => {
    const result = formatLunarDate(15, 1, 2026, false);
    expect(result).toBe('15/01 AL');
  });

  it('should mark leap month', () => {
    const result = formatLunarDate(15, 4, 2026, true);
    expect(result).toContain('nhuận');
  });
});
```

### Hook Tests

```typescript
import { renderHook, act } from '@testing-library/react-hooks';
import { Provider } from 'react-redux';

const wrapper = ({ children }) => (
  <Provider store={createMockStore()}>{children}</Provider>
);

describe('useEvents', () => {
  it('should return events from store', () => {
    const { result } = renderHook(() => useEvents(), { wrapper });
    expect(result.current.events).toEqual([]);
  });
});
```

### Component Tests

```typescript
import { render, fireEvent, waitFor } from '@testing-library/react-native';

describe('EventCard', () => {
  it('should display event name', () => {
    const { getByText } = render(
      <EventCard event={mockEvent} onPress={jest.fn()} />
    );
    expect(getByText('Giỗ ông nội')).toBeTruthy();
  });

  it('should call onPress with event id', () => {
    const onPress = jest.fn();
    const { getByText } = render(
      <EventCard event={mockEvent} onPress={onPress} />
    );
    fireEvent.press(getByText('Giỗ ông nội'));
    expect(onPress).toHaveBeenCalledWith(mockEvent.id);
  });
});
```

### Integration Test (Screen with Redux + Navigation)

```typescript
import { renderWithProviders } from '@test-utils/renderWithProviders';

describe('EventListScreen', () => {
  it('should render list of events', async () => {
    const { getByText } = renderWithProviders(<EventListScreen />, {
      preloadedState: { events: { items: [mockEvent], loading: false } },
    });

    await waitFor(() => {
      expect(getByText('Giỗ ông nội')).toBeTruthy();
    });
  });
});
```

---

## Backend Test Patterns

### Service Unit Tests

```typescript
describe('EventsService', () => {
  let service: EventsService;
  let repository: MockType<Repository<Event>>;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [
        EventsService,
        { provide: getRepositoryToken(Event), useFactory: repositoryMockFactory },
      ],
    }).compile();

    service = module.get(EventsService);
    repository = module.get(getRepositoryToken(Event));
  });

  it('should create event', async () => {
    repository.save.mockResolvedValue(mockEvent);
    const result = await service.create(createDto, userId);
    expect(result.id).toBeDefined();
    expect(repository.save).toHaveBeenCalledWith(expect.objectContaining({ name: createDto.name }));
  });
});
```

### Controller Integration Tests

```typescript
describe('EventsController', () => {
  let app: INestApplication;

  beforeAll(async () => {
    const module = await Test.createTestingModule({
      imports: [EventsModule],
    })
      .overrideProvider(getRepositoryToken(Event))
      .useValue(mockRepository)
      .compile();

    app = module.createNestApplication();
    await app.init();
  });

  it('GET /events should return 200', () => {
    return request(app.getHttpServer())
      .get('/events')
      .set('Authorization', `Bearer ${validToken}`)
      .expect(200);
  });
});
```

### Guard/Pipe Tests

```typescript
describe('JwtAuthGuard', () => {
  it('should allow request with valid token', () => {
    const context = createMockExecutionContext({ headers: { authorization: 'Bearer valid' } });
    expect(guard.canActivate(context)).toBeTruthy();
  });

  it('should reject request without token', () => {
    const context = createMockExecutionContext({ headers: {} });
    expect(() => guard.canActivate(context)).toThrow(UnauthorizedException);
  });
});
```

---

## Mocking Rules

1. **Mock at boundaries** - External APIs, database, file system, OneSignal
2. **Never mock the unit under test**
3. **Use factories** for mock data (not hardcoded objects everywhere)
4. **Reset mocks** in `beforeEach` or `afterEach`
5. **Prefer `jest.fn()` over manual stubs** for tracking calls

### Mock Factories

```typescript
// test/factories/event.factory.ts
export const createMockEvent = (overrides?: Partial<Event>): Event => ({
  id: 'uuid-1',
  name: 'Giỗ ông nội',
  type: EventType.GIO,
  userId: 'user-uuid-1',
  lunarDay: 15,
  lunarMonth: 1,
  lunarYear: 2026,
  isLeapMonth: false,
  createdAt: new Date('2026-01-01'),
  updatedAt: new Date('2026-01-01'),
  deletedAt: null,
  ...overrides,
});
```

---

## Vietnamese Test Fixtures

Use realistic Vietnamese data in tests:

```typescript
export const vietnameseFixtures = {
  events: [
    { name: 'Giỗ ông nội', type: 'gio' },
    { name: 'Sinh nhật mẹ', type: 'sinh_nhat' },
    { name: 'Tết Nguyên Đán', type: 'le' },
    { name: 'Lễ cưới anh Hùng', type: 'ngay_cuoi' },
  ],
  users: [
    { name: 'Nguyễn Văn A', email: 'nguyenvana@example.com' },
    { name: 'Trần Thị B', email: 'tranthib@example.com' },
  ],
};
```
