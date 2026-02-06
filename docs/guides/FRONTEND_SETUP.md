# Frontend Setup Guide

This guide walks you through setting up the React Native mobile app for the Lunar Reminder App.

---

## Prerequisites

- **Node.js** 18+ (LTS recommended)
- **npm** or **pnpm**
- **Xcode** 15+ (for iOS development, macOS only)
- **Android Studio** (for Android development)
- **CocoaPods** (for iOS dependencies)
- **Watchman** (recommended for macOS)

---

## Setup Steps

### 1. Navigate to Frontend Directory

```bash
cd frontend
```

### 2. Install Dependencies

```bash
npm install
```

### 3. iOS Setup (macOS only)

Install CocoaPods dependencies:

```bash
cd ios && pod install && cd ..
```

### 4. Run the App

**iOS (Simulator):**

```bash
npm run ios
```

**Android (Emulator):**

```bash
npm run android
```

---

## Available Commands

| Command | Description |
|---------|-------------|
| `npm run ios` | Run on iOS Simulator |
| `npm run ios:device` | Run on connected iOS device |
| `npm run android` | Run on Android Emulator |
| `npm run android:device` | Run on connected Android device |
| `npm run start` | Start Metro bundler |
| `npm run start:reset` | Start Metro with cache reset |
| `npm test` | Run Jest tests |
| `npm run test:watch` | Run tests in watch mode |
| `npm run test:coverage` | Generate coverage report |
| `npm run lint` | Run ESLint |
| `npm run lint:fix` | Fix ESLint issues |
| `npm run type-check` | TypeScript type checking |

---

## Environment Configuration

Create environment files for different builds:

```bash
# Development
cp .env.example .env.development

# Production
cp .env.example .env.production
```

Configure API endpoint:

```env
API_URL=http://localhost:3000
ONESIGNAL_APP_ID=your-onesignal-app-id
```

---

## Debugging

### Flipper

1. Download [Flipper](https://fbflipper.com/)
2. Run your app
3. Flipper will auto-detect the running app

**Features:**
- Network inspector
- React DevTools
- Layout inspector
- Database viewer

### React Native Debugger

1. Install: `brew install react-native-debugger`
2. Open React Native Debugger
3. In app, open Dev Menu → Debug with Chrome

### Console Logs

```bash
# iOS
npx react-native log-ios

# Android
npx react-native log-android
```

---

## Troubleshooting

### Metro Bundler Issues

```bash
# Clear Metro cache
npm run start:reset

# Or manually
npx react-native start --reset-cache
```

### iOS Build Failures

```bash
# Clean Xcode build
cd ios && xcodebuild clean && cd ..

# Reinstall pods
cd ios && rm -rf Pods Podfile.lock && pod install && cd ..
```

### Android Build Failures

```bash
# Clean Gradle
cd android && ./gradlew clean && cd ..

# Clear Gradle cache
rm -rf ~/.gradle/caches/
```

### "Unable to load script"

1. Check if Metro is running
2. Verify device/emulator can reach Metro:
   - iOS Simulator: Should work automatically
   - Android Emulator: Run `adb reverse tcp:8081 tcp:8081`

### CocoaPods Not Found

```bash
sudo gem install cocoapods
```

---

## Project Structure

```
frontend/src/
├── App.tsx                 # Root component
├── components/
│   ├── common/             # Reusable UI components
│   ├── events/             # Event-related components
│   └── groups/             # Group-related components
├── hooks/                  # Custom React hooks
├── i18n/                   # Internationalization
├── navigation/             # React Navigation setup
├── screens/
│   ├── auth/               # Login, Register, Onboarding
│   ├── calendar/           # Calendar views
│   ├── events/             # Event CRUD screens
│   ├── groups/             # Group management
│   ├── home/               # Dashboard
│   ├── search/             # Search functionality
│   ├── settings/           # App settings
│   └── share/              # Sharing functionality
├── services/               # API services
├── store/                  # Redux store setup
│   └── slices/             # Redux slices
├── types/                  # TypeScript definitions
└── utils/                  # Utility functions
```

---

## Path Aliases

The project uses TypeScript path aliases for cleaner imports:

```typescript
// Instead of
import Button from '../../../components/common/Button';

// Use
import Button from '@components/common/Button';
```

Available aliases:
- `@components/*` → `src/components/*`
- `@screens/*` → `src/screens/*`
- `@services/*` → `src/services/*`
- `@store/*` → `src/store/*`
- `@hooks/*` → `src/hooks/*`
- `@utils/*` → `src/utils/*`
- `@types/*` → `src/types/*`
- `@i18n/*` → `src/i18n/*`
- `@navigation/*` → `src/navigation/*`

---

## Testing

Run unit tests:

```bash
npm test
```

Run with coverage:

```bash
npm run test:coverage
```

See [Frontend Testing Plan](../testing/FRONTEND_TESTING_PLAN.md) for detailed testing strategy.

---

**See Also:**
- [UI/UX Specification](../ui-ux/index.md)
- [Frontend Architecture](../architecture/frontend-architecture.md)
- [Testing Plan](../testing/FRONTEND_TESTING_PLAN.md)
