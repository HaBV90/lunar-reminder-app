# Frontend Development Skill

This skill provides patterns and templates for React Native + TypeScript development in the Lunar Reminder App.

## Component Patterns

### Container/Presenter Pattern

**Container (Smart Component):**
```typescript
// screens/Event/EventListContainer.tsx
import React, { useEffect } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { RootState, AppDispatch } from '@store/index';
import { fetchEvents, selectEvents, selectEventsLoading } from '@store/slices/eventSlice';
import { EventListPresenter } from './EventListPresenter';

export const EventListContainer: React.FC = () => {
  const dispatch = useDispatch<AppDispatch>();
  const events = useSelector(selectEvents);
  const isLoading = useSelector(selectEventsLoading);

  useEffect(() => {
    dispatch(fetchEvents());
  }, [dispatch]);

  const handleEventPress = (id: string) => {
    // Navigate to detail
  };

  const handleRefresh = () => {
    dispatch(fetchEvents());
  };

  return (
    <EventListPresenter
      events={events}
      isLoading={isLoading}
      onEventPress={handleEventPress}
      onRefresh={handleRefresh}
    />
  );
};
```

**Presenter (Dumb Component):**
```typescript
// screens/Event/EventListPresenter.tsx
import React from 'react';
import { FlatList, RefreshControl } from 'react-native';
import { Event } from '@types/event';
import { EventCard } from '@components/event/EventCard';
import { Loading } from '@components/common/Loading';

interface EventListPresenterProps {
  events: Event[];
  isLoading: boolean;
  onEventPress: (id: string) => void;
  onRefresh: () => void;
}

export const EventListPresenter: React.FC<EventListPresenterProps> = ({
  events,
  isLoading,
  onEventPress,
  onRefresh,
}) => {
  if (isLoading && events.length === 0) {
    return <Loading />;
  }

  return (
    <FlatList
      data={events}
      keyExtractor={(item) => item.id}
      renderItem={({ item }) => (
        <EventCard event={item} onPress={() => onEventPress(item.id)} />
      )}
      refreshControl={
        <RefreshControl refreshing={isLoading} onRefresh={onRefresh} />
      }
    />
  );
};
```

## Redux Toolkit Slice Template

```typescript
// store/slices/eventSlice.ts
import { createSlice, createAsyncThunk, PayloadAction } from '@reduxjs/toolkit';
import { Event, CreateEventDto, UpdateEventDto } from '@types/event';
import { eventService } from '@services/eventService';
import { RootState } from '@store/index';

interface EventState {
  items: Event[];
  selectedEvent: Event | null;
  isLoading: boolean;
  error: string | null;
}

const initialState: EventState = {
  items: [],
  selectedEvent: null,
  isLoading: false,
  error: null,
};

// Async thunks
export const fetchEvents = createAsyncThunk(
  'events/fetchAll',
  async (_, { rejectWithValue }) => {
    try {
      const response = await eventService.getAll();
      return response.data.events;
    } catch (error: any) {
      return rejectWithValue(error.response?.data?.message || 'Failed to fetch events');
    }
  }
);

export const createEvent = createAsyncThunk(
  'events/create',
  async (dto: CreateEventDto, { rejectWithValue }) => {
    try {
      const response = await eventService.create(dto);
      return response.data;
    } catch (error: any) {
      return rejectWithValue(error.response?.data?.message || 'Failed to create event');
    }
  }
);

export const updateEvent = createAsyncThunk(
  'events/update',
  async ({ id, dto }: { id: string; dto: UpdateEventDto }, { rejectWithValue }) => {
    try {
      const response = await eventService.update(id, dto);
      return response.data;
    } catch (error: any) {
      return rejectWithValue(error.response?.data?.message || 'Failed to update event');
    }
  }
);

export const deleteEvent = createAsyncThunk(
  'events/delete',
  async (id: string, { rejectWithValue }) => {
    try {
      await eventService.delete(id);
      return id;
    } catch (error: any) {
      return rejectWithValue(error.response?.data?.message || 'Failed to delete event');
    }
  }
);

const eventSlice = createSlice({
  name: 'events',
  initialState,
  reducers: {
    setSelectedEvent: (state, action: PayloadAction<Event | null>) => {
      state.selectedEvent = action.payload;
    },
    clearError: (state) => {
      state.error = null;
    },
  },
  extraReducers: (builder) => {
    builder
      // Fetch events
      .addCase(fetchEvents.pending, (state) => {
        state.isLoading = true;
        state.error = null;
      })
      .addCase(fetchEvents.fulfilled, (state, action) => {
        state.isLoading = false;
        state.items = action.payload;
      })
      .addCase(fetchEvents.rejected, (state, action) => {
        state.isLoading = false;
        state.error = action.payload as string;
      })
      // Create event
      .addCase(createEvent.fulfilled, (state, action) => {
        state.items.push(action.payload);
      })
      // Update event
      .addCase(updateEvent.fulfilled, (state, action) => {
        const index = state.items.findIndex((e) => e.id === action.payload.id);
        if (index !== -1) {
          state.items[index] = action.payload;
        }
      })
      // Delete event
      .addCase(deleteEvent.fulfilled, (state, action) => {
        state.items = state.items.filter((e) => e.id !== action.payload);
      });
  },
});

// Actions
export const { setSelectedEvent, clearError } = eventSlice.actions;

// Selectors
export const selectEvents = (state: RootState) => state.events.items;
export const selectSelectedEvent = (state: RootState) => state.events.selectedEvent;
export const selectEventsLoading = (state: RootState) => state.events.isLoading;
export const selectEventsError = (state: RootState) => state.events.error;

export default eventSlice.reducer;
```

## Navigation Patterns

### Stack Navigator with TypeScript

```typescript
// navigation/types.ts
import { NativeStackScreenProps } from '@react-navigation/native-stack';
import { BottomTabScreenProps } from '@react-navigation/bottom-tabs';
import { CompositeScreenProps } from '@react-navigation/native';

// Root Stack
export type RootStackParamList = {
  Auth: undefined;
  Main: undefined;
  EventDetail: { id: string };
  CreateEvent: undefined;
  EditEvent: { id: string };
};

// Auth Stack
export type AuthStackParamList = {
  Login: undefined;
  Register: undefined;
  Onboarding: undefined;
};

// Main Tab
export type MainTabParamList = {
  Home: undefined;
  Calendar: undefined;
  Groups: undefined;
  Settings: undefined;
};

// Screen props
export type RootStackScreenProps<T extends keyof RootStackParamList> =
  NativeStackScreenProps<RootStackParamList, T>;

export type AuthStackScreenProps<T extends keyof AuthStackParamList> =
  NativeStackScreenProps<AuthStackParamList, T>;

export type MainTabScreenProps<T extends keyof MainTabParamList> =
  CompositeScreenProps<
    BottomTabScreenProps<MainTabParamList, T>,
    RootStackScreenProps<keyof RootStackParamList>
  >;
```

### Navigator Setup

```typescript
// navigation/RootNavigator.tsx
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { useSelector } from 'react-redux';
import { selectIsAuthenticated } from '@store/slices/authSlice';
import { AuthNavigator } from './AuthNavigator';
import { MainNavigator } from './MainNavigator';
import { EventDetailScreen } from '@screens/Event/EventDetailScreen';
import { CreateEventScreen } from '@screens/Event/CreateEventScreen';
import { RootStackParamList } from './types';

const Stack = createNativeStackNavigator<RootStackParamList>();

export const RootNavigator: React.FC = () => {
  const isAuthenticated = useSelector(selectIsAuthenticated);

  return (
    <NavigationContainer>
      <Stack.Navigator screenOptions={{ headerShown: false }}>
        {!isAuthenticated ? (
          <Stack.Screen name="Auth" component={AuthNavigator} />
        ) : (
          <>
            <Stack.Screen name="Main" component={MainNavigator} />
            <Stack.Screen
              name="EventDetail"
              component={EventDetailScreen}
              options={{ headerShown: true }}
            />
            <Stack.Screen
              name="CreateEvent"
              component={CreateEventScreen}
              options={{ headerShown: true, presentation: 'modal' }}
            />
          </>
        )}
      </Stack.Navigator>
    </NavigationContainer>
  );
};
```

## Path Aliases

Configure in `tsconfig.json`:
```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@components/*": ["src/components/*"],
      "@screens/*": ["src/screens/*"],
      "@services/*": ["src/services/*"],
      "@store/*": ["src/store/*"],
      "@hooks/*": ["src/hooks/*"],
      "@utils/*": ["src/utils/*"],
      "@types/*": ["src/types/*"],
      "@constants/*": ["src/constants/*"],
      "@assets/*": ["src/assets/*"],
      "@i18n/*": ["src/i18n/*"],
      "@database/*": ["src/database/*"],
      "@navigation/*": ["src/navigation/*"]
    }
  }
}
```

Configure in `babel.config.js`:
```javascript
module.exports = {
  presets: ['module:@react-native/babel-preset'],
  plugins: [
    [
      'module-resolver',
      {
        root: ['./src'],
        extensions: ['.ios.js', '.android.js', '.js', '.ts', '.tsx', '.json'],
        alias: {
          '@components': './src/components',
          '@screens': './src/screens',
          '@services': './src/services',
          '@store': './src/store',
          '@hooks': './src/hooks',
          '@utils': './src/utils',
          '@types': './src/types',
          '@constants': './src/constants',
          '@assets': './src/assets',
          '@i18n': './src/i18n',
          '@database': './src/database',
          '@navigation': './src/navigation',
        },
      },
    ],
  ],
};
```

## Custom Hooks

```typescript
// hooks/useEvents.ts
import { useCallback, useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { AppDispatch } from '@store/index';
import {
  fetchEvents,
  createEvent,
  updateEvent,
  deleteEvent,
  selectEvents,
  selectEventsLoading,
  selectEventsError,
} from '@store/slices/eventSlice';
import { CreateEventDto, UpdateEventDto } from '@types/event';

export const useEvents = () => {
  const dispatch = useDispatch<AppDispatch>();
  const events = useSelector(selectEvents);
  const isLoading = useSelector(selectEventsLoading);
  const error = useSelector(selectEventsError);

  const refresh = useCallback(() => {
    dispatch(fetchEvents());
  }, [dispatch]);

  const create = useCallback(
    (dto: CreateEventDto) => {
      return dispatch(createEvent(dto)).unwrap();
    },
    [dispatch]
  );

  const update = useCallback(
    (id: string, dto: UpdateEventDto) => {
      return dispatch(updateEvent({ id, dto })).unwrap();
    },
    [dispatch]
  );

  const remove = useCallback(
    (id: string) => {
      return dispatch(deleteEvent(id)).unwrap();
    },
    [dispatch]
  );

  useEffect(() => {
    refresh();
  }, [refresh]);

  return {
    events,
    isLoading,
    error,
    refresh,
    create,
    update,
    remove,
  };
};
```

## Styling with StyleSheet

```typescript
// components/common/Button/Button.styles.ts
import { StyleSheet } from 'react-native';
import { colors, spacing, typography } from '@constants/theme';

export const styles = StyleSheet.create({
  container: {
    borderRadius: 8,
    paddingVertical: spacing.md,
    paddingHorizontal: spacing.lg,
    alignItems: 'center',
    justifyContent: 'center',
  },
  primary: {
    backgroundColor: colors.primary,
  },
  secondary: {
    backgroundColor: colors.secondary,
  },
  outline: {
    backgroundColor: 'transparent',
    borderWidth: 1,
    borderColor: colors.primary,
  },
  disabled: {
    opacity: 0.5,
  },
  text: {
    ...typography.button,
    color: colors.white,
  },
  textOutline: {
    color: colors.primary,
  },
});
```

## Form Handling with React Hook Form

```typescript
// screens/Event/CreateEventScreen.tsx
import React from 'react';
import { View } from 'react-native';
import { useForm, Controller } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import * as yup from 'yup';
import { Input, Button, Select } from '@components/common';
import { useEvents } from '@hooks/useEvents';
import { CreateEventDto, EventType, CalendarType } from '@types/event';

const schema = yup.object({
  name: yup.string().required('Tên sự kiện là bắt buộc').max(200),
  type: yup.string().oneOf(Object.values(EventType)).required(),
  calendarType: yup.string().oneOf(Object.values(CalendarType)).required(),
  lunarDay: yup.number().min(1).max(30).when('calendarType', {
    is: CalendarType.LUNAR,
    then: (schema) => schema.required('Ngày âm là bắt buộc'),
  }),
  lunarMonth: yup.number().min(1).max(12).when('calendarType', {
    is: CalendarType.LUNAR,
    then: (schema) => schema.required('Tháng âm là bắt buộc'),
  }),
});

export const CreateEventScreen: React.FC = () => {
  const { create } = useEvents();
  const { control, handleSubmit, formState: { errors } } = useForm<CreateEventDto>({
    resolver: yupResolver(schema),
    defaultValues: {
      type: EventType.GIO,
      calendarType: CalendarType.LUNAR,
    },
  });

  const onSubmit = async (data: CreateEventDto) => {
    try {
      await create(data);
      // Navigate back
    } catch (error) {
      // Handle error
    }
  };

  return (
    <View>
      <Controller
        control={control}
        name="name"
        render={({ field: { onChange, value } }) => (
          <Input
            label="Tên sự kiện"
            value={value}
            onChangeText={onChange}
            error={errors.name?.message}
          />
        )}
      />
      {/* More form fields */}
      <Button title="Tạo sự kiện" onPress={handleSubmit(onSubmit)} />
    </View>
  );
};
```
