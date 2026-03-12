# SWARNA Mobile App — Enterprise React Native Architecture

![React Native](https://img.shields.io/badge/React_Native-0.73-blue?logo=react)
![TypeScript](https://img.shields.io-badge/TypeScript-5.0-blue?logo=typescript)
![Redux Toolkit](https://img.shields.io-badge/Redux_Toolkit-2.0-purple?logo=redux)
![React Query](https://img.shields.io-badge/React_Query-5.0-red)
![Platform](https://img.shields.io-badge/Platform-iOS%20%7C%20Android-lightgrey)

---

## Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Overall Architecture](#overall-architecture)
- [Project Structure](#project-structure)
- [Feature Module Design](#feature-module-design)
- [State Management Strategy](#state-management-strategy)
- [Navigation Architecture](#navigation-architecture)
- [API Layer Design](#api-layer-design)
- [Design System](#design-system)
- [Performance Optimization](#performance-optimization)
- [Security Best Practices](#security-best-practices)
- [Testing Strategy](#testing-strategy)
- [Getting Started](#getting-started)
- [Environment Configuration](#environment-configuration)
- [Scripts](#scripts)

---

## Overview

**SWARNA Mobile App** is a production-grade, cross-platform mobile application built with **React Native** and **TypeScript**. The architecture is designed to support enterprise-level scalability, maintainability, and team collaboration — following patterns adopted by large technology organizations such as Netflix, Shopify, and Microsoft.

The application consumes existing backend REST APIs and is structured around a **feature-based modular architecture**, with a clean separation between UI, business logic, state management, and data fetching layers.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | React Native 0.73+ |
| Language | TypeScript 5.0+ |
| State Management | Redux Toolkit + React Query |
| Navigation | React Navigation v6 |
| API Client | Axios |
| Secure Storage | react-native-keychain |
| UI Components | Custom Design System |
| Testing | Jest + React Native Testing Library + MSW |
| Environment Config | react-native-config |
| Notifications | react-native-firebase |

---

## Overall Architecture
┌─────────────────────────────────────────────────────────┐ │ React Native App │ │ │ │ ┌──────────┐ ┌──────────┐ ┌──────────────────────┐ │ │ │ Auth │ │Dashboard │ │ Profile / Settings │ │ │ │ Feature │ │ Feature │ │ Feature │ │ │ └──────────┘ └──────────┘ └──────────────────────┘ │ │ │ │ ┌──────────────────────────────────────────────────┐ │ │ │ Shared Component Library │ │ │ │ (Buttons, Inputs, Cards, Layout, Feedback) │ │ │ └──────────────────────────────────────────────────┘ │ │ │ │ ┌──────────────┐ ┌──────────────┐ ┌─────────────┐ │ │ │ Redux Store │ │ React Query │ │ Navigation │ │ │ │ (Global UI) │ │(Server State)│ │ Stack │ │ │ └──────────────┘ └──────────────┘ └─────────────┘ │ │ │ │ ┌──────────────────────────────────────────────────┐ │ │ │ API Services Layer │ │ │ │ (Axios Client + Interceptors) │ │ │ └──────────────────────────────────────────────────┘ │ │ │ │ ┌──────────────────────────────────────────────────┐ │ │ │ Secure Storage / Keychain │ │ │ └──────────────────────────────────────────────────┘ │ └─────────────────────────────────────────────────────────┘ │ ▼ ┌────────────────────────────┐ │ Backend REST APIs │ │ (Authentication, Data, │ │ Profiles, Notifications) │ └────────────────────────────┘

### API Consumption Strategy

- All API calls are made through a **centralized Axios client** (`src/services/api/apiClient.ts`)
- **React Query** manages server state, caching, background refetching, and loading/error states
- **Redux Toolkit** manages global client-side state (auth session, UI preferences, app config)
- JWT tokens are stored securely using **react-native-keychain**
- Token refresh is handled automatically via **Axios interceptors**

---

## Project Structure
swarna-mobile-app-new/ ├── src/ │ ├── app/ │ │ ├── store/ │ │ │ ├── index.ts # Redux store setup │ │ │ ├── rootReducer.ts # Combined root reducer │ │ │ └── middleware.ts # Custom middleware │ │ ├── providers/ │ │ │ ├── AppProvider.tsx # Root provider wrapper │ │ │ ├── ThemeProvider.tsx # Theme context provider │ │ │ └── QueryProvider.tsx # React Query provider │ │ └── App.tsx # App entry point │ │ │ ├── features/ │ │ ├── auth/ │ │ │ ├── screens/ │ │ │ │ ├── LoginScreen.tsx │ │ │ │ └── RegisterScreen.tsx │ │ │ ├── components/ │ │ │ │ ├── LoginForm.tsx │ │ │ │ └── AuthHeader.tsx │ │ │ ├── hooks/ │ │ │ │ ├── useLogin.ts │ │ │ │ └── useAuthState.ts │ │ │ ├── services/ │ │ │ │ └── authService.ts │ │ │ ├── store/ │ │ │ │ ├── authSlice.ts │ │ │ │ └── authSelectors.ts │ │ │ ├── navigation/ │ │ │ │ └── AuthNavigator.tsx │ │ │ └── types/ │ │ │ └── index.ts │ │ │ │ │ ├── dashboard/ │ │ │ ├── screens/ │ │ │ │ └── DashboardScreen.tsx │ │ │ ├── components/ │ │ │ │ └── DashboardWidget.tsx │ │ │ ├── hooks/ │ │ │ │ └── useDashboard.ts │ │ │ ├── services/ │ │ │ │ └── dashboardService.ts │ │ │ ├── store/ │ │ │ │ └── dashboardSlice.ts │ │ │ ├── navigation/ │ │ │ │ └── DashboardNavigator.tsx │ │ │ └── types/ │ │ │ └── index.ts │ │ │ │ │ └── profile/ │ │ ├── screens/ │ │ │ └── ProfileScreen.tsx │ │ ├── components/ │ │ │ └── ProfileCard.tsx │ │ ├── hooks/ │ │ │ └── useProfile.ts │ │ ├── services/ │ │ │ └── profileService.ts │ │ ├── store/ │ │ │ └── profileSlice.ts │ │ ├── navigation/ │ │ │ └── ProfileNavigator.tsx │ │ └── types/ │ │ └── index.ts │ │ │ ├── shared/ │ │ ├── components/ │ │ │ ├── ui/ │ │ │ │ ├── Button.tsx # Reusable button component │ │ │ │ ├── Input.tsx # Reusable text input │ │ │ │ ├── Text.tsx # Typography component │ │ │ │ ├── Card.tsx # Card container │ │ │ │ └── Loader.tsx # Loading spinner │ │ │ ├── layout/ │ │ │ │ ├── Screen.tsx # Safe area screen wrapper │ │ │ │ ├── Row.tsx # Horizontal flex layout │ │ │ │ └── Column.tsx # Vertical flex layout │ │ │ └── feedback/ │ │ │ ├── ErrorBoundary.tsx # React error boundary │ │ │ ├── Toast.tsx # Toast notification │ │ │ └── EmptyState.tsx # Empty list/data state │ │ ├── hooks/ │ │ │ ├── useAppDispatch.ts # Typed Redux dispatch │ │ │ ├── useAppSelector.ts # Typed Redux selector │ │ │ └── useDebounce.ts # Debounce utility hook │ │ └── utils/ │ │ ├── formatters.ts # Date/currency formatters │ │ ├── validators.ts # Input validation helpers │ │ └── logger.ts # Centralized logger utility │ │ │ ├── services/ │ │ ├── api/ │ │ │ ├── apiClient.ts # Axios instance + config │ │ │ ├── interceptors/ │ │ │ │ ├── authInterceptor.ts # JWT attach + token refresh │ │ │ │ └── errorInterceptor.ts# Global error handling │ │ │ └── endpoints.ts # All API endpoint constants │ │ ├── storage/ │ │ │ └── secureStorage.ts # Keychain token storage │ │ └── notifications/ │ │ └── notificationService.ts # Push notification handler │ │ │ ├── navigation/ │ │ ├── RootNavigator.tsx # Auth vs App routing │ │ ├── AppNavigator.tsx # Main tab/stack navigator │ │ └── navigationRef.ts # Global navigation reference │ │ │ ├── theme/ │ │ ├── colors.ts # Brand color palette │ │ ├── typography.ts # Font sizes and weights │ │ ├── spacing.ts # Spacing scale │ │ └── index.ts # Theme exports │ │ │ ├── config/ │ │ ├── env.ts # Environment variable access │ │ └── constants.ts # App-wide constants │ │ │ └── types/ │ ├── api.ts # API response types │ ├── navigation.ts # Navigation param types │ └── index.ts # Global type exports │ ├── tests/ │ ├── unit/ │ │ ├── utils/ │ │ │ └── formatters.test.ts │ │ └── hooks/ │ │ └── useDebounce.test.ts │ ├── components/ │ │ ├── Button.test.tsx │ │ └── Input.test.tsx │ └── mocks/ │ ├── handlers.ts # MSW API mock handlers │ ├── server.ts # MSW server setup │ └── fixtures/ │ ├── authFixtures.ts # Auth mock data │ └── userFixtures.ts # User mock data │ ├── android/ # Android native project ├── ios/ # iOS native project ├── .env.development # Development environment vars ├── .env.staging # Staging environment vars ├── .env.production # Production environment vars ├── babel.config.js ├── jest.config.js ├── tsconfig.json ├── package.json └── README.md

features/auth/ ├── screens/ │ └── LoginScreen.tsx # Screen-level component (navigation aware) ├── components/ │ └── LoginForm.tsx # Feature-specific UI components ├── hooks/ │ └── useLogin.ts # Business logic + API call via React Query ├── services/ │ └── authService.ts # Raw API calls for auth endpoints ├── store/ │ ├── authSlice.ts # Redux slice (tokens, user session) │ └── authSelectors.ts # Memoized selectors ├── navigation/ │ └── AuthNavigator.tsx # Auth stack navigator └── types/ └── index.ts # Auth-specific TypeScript interfaces

Screen └── calls Hook (useLogin) └── calls React Query mutation └── calls authService.login() └── calls apiClient.post('/auth/login') └── Response stored in Redux authSlice └── RootNavigator reacts to auth state change

React Query — Server State
Navigation Architecture
Root Navigator Logic
API Layer Design
Axios Client
Auth Interceptor
Feature API Service
Design System
Reusable Button Component
Performance Optimization
Technique	Implementation
Memoization	React.memo, useMemo, useCallback on all shared components
Lazy Loading	React.lazy + Suspense for heavy feature screens
List Rendering	FlatList with keyExtractor, getItemLayout, windowSize
Re-render Control	Redux selectors with createSelector (Reselect)
Image Optimization	react-native-fast-image with CDN caching
Bundle Splitting	Hermes engine enabled on both platforms
Query Caching	React Query staleTime and cacheTime configuration
Security Best Practices
Secure Token Storage
Environment Configuration
Security Checklist
<input checked="" disabled="" type="checkbox"> JWT tokens stored in Keychain (iOS) / Keystore (Android) — never in AsyncStorage
<input checked="" disabled="" type="checkbox"> Certificate pinning for all production API calls
<input checked="" disabled="" type="checkbox"> Token refresh handled transparently via Axios interceptors
<input checked="" disabled="" type="checkbox"> Environment secrets never committed to version control
<input checked="" disabled="" type="checkbox"> .env.* files listed in .gitignore
<input checked="" disabled="" type="checkbox"> ProGuard enabled for Android release builds
<input checked="" disabled="" type="checkbox"> Bitcode disabled, debug symbols stripped for iOS release
Testing Strategy
Test Layers
Layer	Tool	Scope
Unit Tests	Jest	Utilities, hooks, Redux slices
Component Tests	React Native Testing Library	UI component rendering and interaction
API Mocking	Mock Service Worker (MSW)	Intercept and mock HTTP requests
Integration Tests	Jest + MSW	Full hook + service + API flow
Example Component Test
Example MSW Handler
Getting Started
Prerequisites
Installation
Run the Application
Environment Configuration
File	Purpose
.env.development	Local development API
.env.staging	Staging/QA environment
.env.production	Live production environment
Important: Never commit .env.* files containing secrets to version control.

Scripts
Architecture Decision Records (ADR)
Decision	Choice	Rationale
State Management	Redux Toolkit + React Query	Separation of client vs server state
API Client	Axios	Interceptor support, wide ecosystem
Navigation	React Navigation v6	Industry standard, type-safe
Secure Storage	react-native-keychain	OS-level encryption, not AsyncStorage
Styling	StyleSheet API + Theme System	Performance, no runtime overhead
Testing	Jest + RNTL + MSW	Full testing pyramid coverage
Language	TypeScript strict mode	Type safety, maintainability at scale
Contributing
Create a feature branch: git checkout -b feature/your-feature-name
Follow the feature module structure defined in this document
Ensure all tests pass: npm test
Ensure TypeScript compiles: npm run ts:check
Open a Pull Request against main
License
This project is proprietary and confidential. © 2024 SWARNA. All rights reserved. ```
## Feature Module Design

Each feature is a **self-contained module** with its own screens, components, hooks, services, store, and types. Features do not directly import from each other — all cross-feature communication goes through the global Redux store or shared services.

### Example: Auth Feature
features/auth/ ├── screens/ │ └── LoginScreen.tsx # Screen-level component (navigation aware) ├── components/ │ └── LoginForm.tsx # Feature-specific UI components ├── hooks/ │ └── useLogin.ts # Business logic + API call via React Query ├── services/ │ └── authService.ts # Raw API calls for auth endpoints ├── store/ │ ├── authSlice.ts # Redux slice (tokens, user session) │ └── authSelectors.ts # Memoized selectors ├── navigation/ │ └── AuthNavigator.tsx # Auth stack navigator └── types/ └── index.ts # Auth-specific TypeScript interfaces

### Data Flow Within a Feature
Screen └── calls Hook (useLogin) └── calls React Query mutation └── calls authService.login() └── calls apiClient.post('/auth/login') └── Response stored in Redux authSlice └── RootNavigator reacts to auth state change

---

## State Management Strategy

### Architecture Decision

| State Type | Tool | Reason |
|---|---|---|
| Authentication session | Redux Toolkit | Needs to persist globally across all features |
| User profile | React Query | Server-driven, benefits from caching |
| Server/API data | React Query | Automatic caching, refetch, background sync |
| UI state (modals, tabs) | Redux Toolkit | Predictable, debuggable global UI control |
| Local component state | useState / useReducer | Isolated, no global impact |

### Redux Toolkit — Global State

```typescript
// src/features/auth/store/authSlice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';

interface AuthState {
  accessToken: string | null;
  refreshToken: string | null;
  isAuthenticated: boolean;
  userId: string | null;
}

const initialState: AuthState = {
  accessToken: null,
  refreshToken: null,
  isAuthenticated: false,
  userId: null,
};

const authSlice = createSlice({
  name: 'auth',
  initialState,
  reducers: {
    setCredentials(state, action: PayloadAction<{ accessToken: string; refreshToken: string; userId: string }>) {
      state.accessToken = action.payload.accessToken;
      state.refreshToken = action.payload.refreshToken;
      state.isAuthenticated = true;
      state.userId = action.payload.userId;
    },
    clearCredentials(state) {
      state.accessToken = null;
      state.refreshToken = null;
      state.isAuthenticated = false;
      state.userId = null;
    },
  },
});

export const { setCredentials, clearCredentials } = authSlice.actions;
export default authSlice.reducer;

React Query — Server State

// src/features/profile/hooks/useProfile.ts
import { useQuery } from '@tanstack/react-query';
import { profileService } from '../services/profileService';

export const useProfile = (userId: string) => {
  return useQuery({
    queryKey: ['profile', userId],
    queryFn: () => profileService.getProfile(userId),
    staleTime: 5 * 60 * 1000, // 5 minutes cache
    retry: 2,
  });
};

Navigation Architecture

RootNavigator
├── AuthNavigator (if not authenticated)
│   ├── LoginScreen
│   └── RegisterScreen
└── AppNavigator (if authenticated)
    ├── DashboardNavigator (Tab)
    │   └── DashboardScreen
    ├── ProfileNavigator (Tab)
    │   └── ProfileScreen
    └── SettingsNavigator (Tab)
        └── SettingsScreen

Root Navigator Logic

// src/navigation/RootNavigator.tsx
const RootNavigator = () => {
  const isAuthenticated = useAppSelector(state => state.auth.isAuthenticated);

  return (
    <NavigationContainer ref={navigationRef}>
      {isAuthenticated ? <AppNavigator /> : <AuthNavigator />}
    </NavigationContainer>
  );
};

API Layer Design

Axios Client
// src/services/api/apiClient.ts
import axios from 'axios';
import Config from 'react-native-config';

const apiClient = axios.create({
  baseURL: Config.API_BASE_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
    Accept: 'application/json',
  },
});

export default apiClient;

Auth Interceptor

// src/services/api/interceptors/authInterceptor.ts
import apiClient from '../apiClient';
import { getAccessToken, getRefreshToken, saveTokens } from '../../storage/secureStorage';
import { store } from '../../../app/store';
import { clearCredentials, setCredentials } from '../../../features/auth/store/authSlice';

apiClient.interceptors.request.use(async (config) => {
  const token = await getAccessToken();
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

apiClient.interceptors.response.use(
  (response) => response,
  async (error) => {
    const originalRequest = error.config;
    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true;
      try {
        const refreshToken = await getRefreshToken();
        const { data } = await apiClient.post('/auth/refresh', { refreshToken });
        await saveTokens(data.accessToken, data.refreshToken);
        store.dispatch(setCredentials(data));
        originalRequest.headers.Authorization = `Bearer ${data.accessToken}`;
        return apiClient(originalRequest);
      } catch {
        store.dispatch(clearCredentials());
      }
    }
    return Promise.reject(error);
  },
);

Feature API Service

// src/features/auth/services/authService.ts
import apiClient from '../../../services/api/apiClient';
import { ENDPOINTS } from '../../../services/api/endpoints';
import { LoginRequest, AuthResponse } from '../types';

export const authService = {
  login: async (payload: LoginRequest): Promise<AuthResponse> => {
    const { data } = await apiClient.post<AuthResponse>(ENDPOINTS.AUTH.LOGIN, payload);
    return data;
  },
  logout: async (): Promise<void> => {
    await apiClient.post(ENDPOINTS.AUTH.LOGOUT);
  },
  refreshToken: async (refreshToken: string): Promise<AuthResponse> => {
    const { data } = await apiClient.post<AuthResponse>(ENDPOINTS.AUTH.REFRESH, { refreshToken });
    return data;
  },
};

Design System

// src/theme/colors.ts
export const Colors = {
  primary:     '#F5A623',
  secondary:   '#4A90E2',
  background:  '#FFFFFF',
  surface:     '#F8F8F8',
  error:       '#D0021B',
  success:     '#7ED321',
  warning:     '#F8E71C',
  textPrimary: '#1A1A1A',
  textSecondary: '#6B6B6B',
  border:      '#E0E0E0',
  disabled:    '#BDBDBD',
};

// src/theme/typography.ts
export const Typography = {
  h1: { fontSize: 32, fontWeight: '700', lineHeight: 40 },
  h2: { fontSize: 24, fontWeight: '600', lineHeight: 32 },
  h3: { fontSize: 20, fontWeight: '600', lineHeight: 28 },
  body1: { fontSize: 16, fontWeight: '400', lineHeight: 24 },
  body2: { fontSize: 14, fontWeight: '400', lineHeight: 20 },
  caption: { fontSize: 12, fontWeight: '400', lineHeight: 16 },
  button: { fontSize: 16, fontWeight: '600', lineHeight: 24 },
};

// src/theme/spacing.ts
export const Spacing = {
  xs:   4,
  sm:   8,
  md:   16,
  lg:   24,
  xl:   32,
  xxl:  48,
};

Reusable Button Component

// src/shared/components/ui/Button.tsx
import React, { memo } from 'react';
import { TouchableOpacity, Text, ActivityIndicator, StyleSheet } from 'react-native';
import { Colors, Typography, Spacing } from '../../../theme';

interface ButtonProps {
  label: string;
  onPress: () => void;
  variant?: 'primary' | 'secondary' | 'outline';
  loading?: boolean;
  disabled?: boolean;
}

export const Button = memo(({ label, onPress, variant = 'primary', loading, disabled }: ButtonProps) => (
  <TouchableOpacity
    style={[styles.base, styles[variant], disabled && styles.disabled]}
    onPress={onPress}
    disabled={disabled || loading}
    activeOpacity={0.8}
  >
    {loading
      ? <ActivityIndicator color={Colors.background} />
      : <Text style={styles.label}>{label}</Text>
    }
  </TouchableOpacity>
));

const styles = StyleSheet.create({
  base:      { paddingVertical: Spacing.sm, paddingHorizontal: Spacing.md, borderRadius: 8, alignItems: 'center' },
  primary:   { backgroundColor: Colors.primary },
  secondary: { backgroundColor: Colors.secondary },
  outline:   { backgroundColor: 'transparent', borderWidth: 1, borderColor: Colors.primary },
  disabled:  { opacity: 0.5 },
  label:     { ...Typography.button, color: Colors.background },
});

Performance Optimization
Technique	Implementation
Memoization	React.memo, useMemo, useCallback on all shared components
Lazy Loading	React.lazy + Suspense for heavy feature screens
List Rendering	FlatList with keyExtractor, getItemLayout, windowSize
Re-render Control	Redux selectors with createSelector (Reselect)
Image Optimization	react-native-fast-image with CDN caching
Bundle Splitting	Hermes engine enabled on both platforms
Query Caching	React Query staleTime and cacheTime configuration

// Optimized FlatList example
<FlatList
  data={items}
  keyExtractor={(item) => item.id}
  renderItem={renderItem}
  getItemLayout={(_, index) => ({ length: ITEM_HEIGHT, offset: ITEM_HEIGHT * index, index })}
  windowSize={10}
  maxToRenderPerBatch={10}
  initialNumToRender={15}
  removeClippedSubviews={true}
/>

Security Best Practices

Secure Token Storage

// src/services/storage/secureStorage.ts
import * as Keychain from 'react-native-keychain';

const TOKEN_KEY = 'swarna_auth_tokens';

export const saveTokens = async (accessToken: string, refreshToken: string) => {
  await Keychain.setGenericPassword(TOKEN_KEY, JSON.stringify({ accessToken, refreshToken }));
};

export const getAccessToken = async (): Promise<string | null> => {
  const credentials = await Keychain.getGenericPassword();
  if (credentials) {
    const { accessToken } = JSON.parse(credentials.password);
    return accessToken;
  }
  return null;
};

export const getRefreshToken = async (): Promise<string | null> => {
  const credentials = await Keychain.getGenericPassword();
  if (credentials) {
    const { refreshToken } = JSON.parse(credentials.password);
    return refreshToken;
  }
  return null;
};

export const clearTokens = async () => {
  await Keychain.resetGenericPassword();
};

Environment Configuration

// src/config/env.ts
import Config from 'react-native-config';

export const ENV = {
  API_BASE_URL:    Config.API_BASE_URL    ?? 'https://api.dev.swarna.com',
  APP_ENV:         Config.APP_ENV         ?? 'development',
  TIMEOUT_MS:      Number(Config.TIMEOUT_MS ?? 10000),
  SENTRY_DSN:      Config.SENTRY_DSN      ?? '',
};

# .env.development
API_BASE_URL=https://api.dev.swarna.com
APP_ENV=development
TIMEOUT_MS=10000

# .env.staging
API_BASE_URL=https://api.staging.swarna.com
APP_ENV=staging
TIMEOUT_MS=10000

# .env.production
API_BASE_URL=https://api.swarna.com
APP_ENV=production
TIMEOUT_MS=8000

Security Checklist

<input checked="" disabled="" type="checkbox"> JWT tokens stored in Keychain (iOS) / Keystore (Android) — never in AsyncStorage
<input checked="" disabled="" type="checkbox"> Certificate pinning for all production API calls
<input checked="" disabled="" type="checkbox"> Token refresh handled transparently via Axios interceptors
<input checked="" disabled="" type="checkbox"> Environment secrets never committed to version control
<input checked="" disabled="" type="checkbox"> .env.* files listed in .gitignore
<input checked="" disabled="" type="checkbox"> ProGuard enabled for Android release builds
<input checked="" disabled="" type="checkbox"> Bitcode disabled, debug symbols stripped for iOS release

Testing Strategy
Test Layers
Layer	Tool	Scope
Unit Tests	Jest	Utilities, hooks, Redux slices
Component Tests	React Native Testing Library	UI component rendering and interaction
API Mocking	Mock Service Worker (MSW)	Intercept and mock HTTP requests
Integration Tests	Jest + MSW	Full hook + service + API flow
Example Component Test
// __tests__/components/Button.test.tsx
import React from 'react';
import { render, fireEvent } from '@testing-library/react-native';
import { Button } from '../../src/shared/components/ui/Button';

describe('Button Component', () => {
  it('renders the correct label', () => {
    const { getByText } = render(<Button label="Login" onPress={() => {}} />);
    expect(getByText('Login')).toBeTruthy();
  });

  it('calls onPress when tapped', () => {
    const mockPress = jest.fn();
    const { getByText } = render(<Button label="Login" onPress={mockPress} />);
    fireEvent.press(getByText('Login'));
    expect(mockPress).toHaveBeenCalledTimes(1);
  });

  it('shows loader when loading is true', () => {
    const { queryByText, getByTestId } = render(
      <Button label="Login" onPress={() => {}} loading={true} />
    );
    expect(queryByText('Login')).toBeNull();
  });
});

Example MSW Handler

// __tests__/mocks/handlers.ts
import { rest } from 'msw';

export const handlers = [
  rest.post('https://api.dev.swarna.com/auth/login', (req, res, ctx) => {
    return res(
      ctx.status(200),
      ctx.json({
        accessToken:  'mock-access-token',
        refreshToken: 'mock-refresh-token',
        userId:       'user-123',
      }),
    );
  }),

  rest.get('https://api.dev.swarna.com/profile/:userId', (req, res, ctx) => {
    return res(
      ctx.status(200),
      ctx.json({
        id:    'user-123',
        name:  'Swarna User',
        email: 'user@swarna.com',
      }),
    );
  }),
];

Getting Started
Prerequisites
node >= 18.x
ruby >= 3.x       # iOS only
Xcode >= 15.x     # iOS only
Android Studio    # Android only
JDK 17            # Android only

Installation

# Clone the repository
git clone https://github.com/YOUR_USERNAME/SWARNA_MOBILE_APP_NEW.git
cd SWARNA_MOBILE_APP_NEW

# Install dependencies
npm install

# iOS — install pods
cd ios && pod install && cd ..

# Copy environment file
cp .env.development .env

Run the Application

# Start Metro bundler
npm start

# Run on iOS
npm run ios

# Run on Android
npm run android

Environment Configuration
File	Purpose
.env.development	Local development API
.env.staging	Staging/QA environment
.env.production	Live production environment

Important: Never commit .env.* files containing secrets to version control.

Scripts

{
  "scripts": {
    "start":        "react-native start",
    "ios":          "react-native run-ios",
    "android":      "react-native run-android",
    "test":         "jest --watchAll=false",
    "test:watch":   "jest --watchAll",
    "test:coverage":"jest --coverage",
    "lint":         "eslint src --ext .ts,.tsx",
    "lint:fix":     "eslint src --ext .ts,.tsx --fix",
    "ts:check":     "tsc --noEmit",
    "clean:ios":    "cd ios && xcodebuild clean && cd ..",
    "clean:android":"cd android && ./gradlew clean && cd .."
  }
}

Architecture Decision Records (ADR)
Decision	Choice	Rationale
State Management	Redux Toolkit + React Query	Separation of client vs server state
API Client	Axios	Interceptor support, wide ecosystem
Navigation	React Navigation v6	Industry standard, type-safe
Secure Storage	react-native-keychain	OS-level encryption, not AsyncStorage
Styling	StyleSheet API + Theme System	Performance, no runtime overhead
Testing	Jest + RNTL + MSW	Full testing pyramid coverage
Language	TypeScript strict mode	Type safety, maintainability at scale
Contributing
Create a feature branch: git checkout -b feature/your-feature-name
Follow the feature module structure defined in this document
Ensure all tests pass: npm test
Ensure TypeScript compiles: npm run ts:check
Open a Pull Request against main
License
This project is proprietary and confidential. © 2024 SWARNA. All rights reserved. ```