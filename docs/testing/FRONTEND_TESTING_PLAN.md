# Frontend Testing Plan

Comprehensive testing strategy and sprint schedule for the Lunar Reminder App mobile frontend.

---

## Testing Philosophy

We follow the **Testing Pyramid** approach:

```
        /\
       /E2E\        10% - Critical user journeys
      /------\
     /Integration\   20% - Screen flows, Redux interactions
    /--------------\
   /    Unit Tests   \ 70% - Utils, hooks, components
  /____________________\
```

---

## Testing Tools

### Currently Installed
- **Jest** 29.7 - Test runner and assertion library
- **@testing-library/react-native** 12.4 - Component testing utilities

### To Be Added (Sprint 8)
- **Detox** - End-to-end testing framework
- **MSW** (Mock Service Worker) - API mocking for integration tests

---

## Coverage Goals

| Layer | Target Coverage |
|-------|-----------------|
| Utils | 90%+ |
| Hooks | 80%+ |
| Components | 70%+ |
| Screens | 60%+ |
| **Overall** | **75%+** |

---

## Sprint Schedule

### Sprint 1: Utility Functions (Week 1)

**Focus:** Pure utility functions with no dependencies

| Target File | Tests | Description |
|-------------|-------|-------------|
| `utils/dateUtils.ts` | 6-8 | Date formatting, lunar-solar conversion helpers |
| `utils/validationUtils.ts` | 4-6 | Form validation rules |
| `utils/colorUtils.ts` | 3-4 | Color manipulation, event colors |
| `utils/formatUtils.ts` | 2-3 | Text formatting helpers |

**Estimated Tests:** 15-20

**Example:**
```typescript
// dateUtils.test.ts
describe('dateUtils', () => {
  describe('formatLunarDate', () => {
    it('should format lunar date in Vietnamese', () => {
      expect(formatLunarDate(15, 1, 2024)).toBe('15 tháng Giêng, 2024');
    });
  });
});
```

---

### Sprint 2: Custom Hooks (Week 2)

**Focus:** Custom React hooks

| Target File | Tests | Description |
|-------------|-------|-------------|
| `hooks/useAuth.ts` | 5-7 | Auth state, login/logout |
| `hooks/useEvents.ts` | 6-8 | Event CRUD operations |
| `hooks/useGroups.ts` | 5-6 | Group management |
| `hooks/useLunarDate.ts` | 4-5 | Lunar date calculations |

**Estimated Tests:** 20-25

**Example:**
```typescript
// useAuth.test.ts
describe('useAuth', () => {
  it('should return isAuthenticated false when no token', () => {
    const { result } = renderHook(() => useAuth());
    expect(result.current.isAuthenticated).toBe(false);
  });
});
```

---

### Sprint 3: Common Components (Week 3)

**Focus:** Reusable UI components

| Target File | Tests | Description |
|-------------|-------|-------------|
| `components/common/Button.tsx` | 3-4 | Variants, disabled state, press handling |
| `components/common/Input.tsx` | 4-5 | Validation, error display, focus states |
| `components/common/Card.tsx` | 2-3 | Rendering, press handling |
| `components/common/Loading.tsx` | 2 | Loading states |
| `components/common/Text.tsx` | 2-3 | Variants, styling |
| `components/common/SafeArea.tsx` | 2 | Platform-specific rendering |

**Estimated Tests:** 15-20

**Example:**
```typescript
// Button.test.tsx
describe('Button', () => {
  it('should call onPress when pressed', () => {
    const onPress = jest.fn();
    const { getByText } = render(<Button title="Submit" onPress={onPress} />);
    fireEvent.press(getByText('Submit'));
    expect(onPress).toHaveBeenCalledTimes(1);
  });
});
```

---

### Sprint 4: Feature Components (Week 4)

**Focus:** Domain-specific components

| Target File | Tests | Description |
|-------------|-------|-------------|
| `components/events/EventCard.tsx` | 6-8 | Event display, dates, actions |
| `components/groups/GroupCard.tsx` | 4-5 | Group info, member count |
| `components/calendar/CalendarDay.tsx` | 4-5 | Day selection, lunar dates |

**Estimated Tests:** 15-20

**Example:**
```typescript
// EventCard.test.tsx
describe('EventCard', () => {
  it('should display lunar date when event is lunar type', () => {
    const event = mockLunarEvent();
    const { getByText } = render(<EventCard event={event} />);
    expect(getByText(/tháng/)).toBeTruthy();
  });
});
```

---

### Sprint 5: Auth Screens (Week 5)

**Focus:** Authentication flow screens

| Target File | Tests | Description |
|-------------|-------|-------------|
| `screens/auth/LoginScreen.tsx` | 4-5 | Form validation, submit, error handling |
| `screens/auth/RegisterScreen.tsx` | 4-5 | Validation, registration flow |
| `screens/auth/OnboardingScreen.tsx` | 3-4 | Slide navigation, skip/continue |

**Estimated Tests:** 10-15

**Example:**
```typescript
// LoginScreen.test.tsx
describe('LoginScreen', () => {
  it('should show validation error for invalid email', async () => {
    const { getByPlaceholderText, getByText } = render(<LoginScreen />);
    fireEvent.changeText(getByPlaceholderText('Email'), 'invalid');
    fireEvent.press(getByText('Đăng nhập'));
    await waitFor(() => {
      expect(getByText('Email không hợp lệ')).toBeTruthy();
    });
  });
});
```

---

### Sprint 6: Event Screens (Week 6)

**Focus:** Core event management screens

| Target File | Tests | Description |
|-------------|-------|-------------|
| `screens/events/EventListScreen.tsx` | 4-5 | List rendering, filtering |
| `screens/events/EventDetailScreen.tsx` | 3-4 | Event display, actions |
| `screens/events/CreateEventScreen.tsx` | 5-6 | Form, lunar/solar toggle, validation |
| `screens/events/EditEventScreen.tsx` | 3-4 | Edit flow, save changes |

**Estimated Tests:** 15-20

---

### Sprint 7: Integration Tests (Week 7)

**Focus:** Cross-component and Redux integration

| Test Suite | Tests | Description |
|------------|-------|-------------|
| Auth Flow | 3-4 | Login → Home navigation |
| Event CRUD | 4-5 | Create → List → Edit → Delete |
| Group Flow | 3-4 | Create group → Add event |
| Navigation | 3-4 | Deep linking, tab navigation |

**Estimated Tests:** 10-15

**Example:**
```typescript
// authFlow.integration.test.tsx
describe('Auth Flow Integration', () => {
  it('should navigate to Home after successful login', async () => {
    const { getByPlaceholderText, getByText } = render(<App />);

    fireEvent.changeText(getByPlaceholderText('Email'), 'test@example.com');
    fireEvent.changeText(getByPlaceholderText('Password'), 'password123');
    fireEvent.press(getByText('Đăng nhập'));

    await waitFor(() => {
      expect(getByText('Sự kiện sắp tới')).toBeTruthy();
    });
  });
});
```

---

### Sprint 8: E2E Setup (Week 8)

**Focus:** Detox configuration and first E2E tests

**Tasks:**
1. Install and configure Detox
2. Set up iOS and Android build configurations
3. Create test helpers and fixtures
4. Write first E2E test (happy path)

| Test Suite | Tests | Description |
|------------|-------|-------------|
| App Launch | 1-2 | Cold start, splash screen |
| Auth E2E | 2-3 | Complete login/logout flow |
| Event Creation E2E | 2-3 | Create lunar event end-to-end |

**Estimated Tests:** 5-10

**Example:**
```typescript
// app.e2e.ts
describe('App E2E', () => {
  beforeAll(async () => {
    await device.launchApp();
  });

  it('should show onboarding for first launch', async () => {
    await expect(element(by.text('Chào mừng'))).toBeVisible();
  });
});
```

---

## Test File Structure

```
frontend/src/
├── __tests__/
│   ├── setup.ts              # Jest setup
│   ├── mocks/                # Mock data and services
│   │   ├── events.ts
│   │   ├── users.ts
│   │   └── api.ts
│   ├── utils/
│   │   ├── dateUtils.test.ts
│   │   ├── validationUtils.test.ts
│   │   └── colorUtils.test.ts
│   ├── hooks/
│   │   ├── useAuth.test.ts
│   │   ├── useEvents.test.ts
│   │   └── useLunarDate.test.ts
│   ├── components/
│   │   ├── common/
│   │   │   ├── Button.test.tsx
│   │   │   └── Input.test.tsx
│   │   └── events/
│   │       └── EventCard.test.tsx
│   ├── screens/
│   │   ├── auth/
│   │   │   └── LoginScreen.test.tsx
│   │   └── events/
│   │       └── EventListScreen.test.tsx
│   └── integration/
│       ├── authFlow.test.tsx
│       └── eventCrud.test.tsx
├── e2e/
│   ├── app.e2e.ts
│   ├── auth.e2e.ts
│   └── events.e2e.ts
```

---

## Running Tests

```bash
# Run all tests
npm test

# Run with coverage
npm run test:coverage

# Run specific test file
npm test -- dateUtils.test.ts

# Run in watch mode
npm run test:watch

# Run E2E tests (after Sprint 8)
npm run e2e:ios
npm run e2e:android
```

---

## CI Integration

Tests will run automatically on:
- Pull request creation
- Push to feature branches
- Merge to main/develop

**CI Pipeline Steps:**
1. Install dependencies
2. Run linting
3. Run type checking
4. Run unit tests
5. Generate coverage report
6. Fail if coverage below threshold

---

## Summary

| Sprint | Week | Focus | Est. Tests |
|--------|------|-------|------------|
| 1 | W1 | Utility Functions | 15-20 |
| 2 | W2 | Custom Hooks | 20-25 |
| 3 | W3 | Common Components | 15-20 |
| 4 | W4 | Feature Components | 15-20 |
| 5 | W5 | Auth Screens | 10-15 |
| 6 | W6 | Event Screens | 15-20 |
| 7 | W7 | Integration Tests | 10-15 |
| 8 | W8 | E2E Setup | 5-10 |
| **Total** | | | **105-145** |

---

**See Also:**
- [Frontend Setup Guide](../guides/FRONTEND_SETUP.md)
- [Frontend Architecture](../architecture/frontend-architecture.md)
