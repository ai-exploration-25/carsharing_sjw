# 카셰어링 서비스 Frontend Architecture Document

## Template and Framework Selection

### Framework Decision

**Selected Framework**: Next.js 14 with React 18 and TypeScript
- **Rationale**: Server-side rendering for SEO, excellent performance, robust ecosystem
- **Starter Template**: None - custom implementation for maximum control
- **UI Framework**: Tailwind CSS + Headless UI for Korean market optimization

**Analysis of Requirements**:
- **Mobile-first responsive design** for Korean users (80% mobile usage)
- **Hybrid B2B/B2C interface** requiring different UX patterns
- **Real-time features** for booking notifications and status updates
- **Korean service integrations** (Kakao Map, Toss Payments)

### Change Log

| Date | Version | Description | Author |
|------|---------|-------------|---------|
| 2025-09-20 | v1.0 | Initial frontend architecture creation | Frontend Architect |

## Frontend Tech Stack

### Technology Stack Table

| Category | Technology | Version | Purpose | Rationale |
|----------|------------|---------|---------|-----------|
| Framework | Next.js | 14.0.4 | React meta-framework with SSR/SSG | Excellent performance, SEO benefits, Korean market proven |
| UI Library | React | 18.2.0 | Component-based UI library | Industry standard, excellent ecosystem, team expertise |
| State Management | Zustand | 4.4.7 | Lightweight state management | Simple API, TypeScript support, better than Redux for MVP |
| Routing | Next.js Router | 14.0.4 | File-based routing system | Built-in, app directory support, excellent DX |
| Build Tool | Next.js | 14.0.4 | Integrated build system | Zero-config, optimized for React, excellent performance |
| Styling | Tailwind CSS | 3.3.6 | Utility-first CSS framework | Rapid development, consistent design system, mobile-first |
| Testing | Jest + Testing Library | 29.7.0 + 14.1.2 | Component and unit testing | React-focused testing, excellent mocking capabilities |
| Component Library | Headless UI | 1.7.17 | Unstyled accessible components | Accessibility built-in, Tailwind compatible, flexible |
| Form Handling | React Hook Form | 7.48.2 | Performant form management | Minimal re-renders, excellent validation, TypeScript support |
| Animation | Framer Motion | 10.16.16 | Motion library for React | Smooth animations, gesture support, excellent performance |
| Dev Tools | ESLint + Prettier | 8.55.0 + 3.1.1 | Code quality and formatting | Consistent code style, error prevention, team collaboration |

## Project Structure

```
frontend/
├── public/                         # Static assets
│   ├── images/
│   │   ├── cars/                   # Vehicle placeholder images
│   │   ├── icons/                  # Custom icons and logos
│   │   └── avatars/                # Default profile images
│   ├── favicon/                    # Favicon variations
│   └── manifest.json               # PWA manifest
├── src/
│   ├── app/                        # Next.js 14 app directory
│   │   ├── (auth)/                 # Route groups for auth pages
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   ├── register/
│   │   │   │   └── page.tsx
│   │   │   └── layout.tsx          # Auth layout wrapper
│   │   ├── (dashboard)/            # Protected dashboard routes
│   │   │   ├── host/               # Host-specific pages
│   │   │   │   ├── vehicles/
│   │   │   │   ├── bookings/
│   │   │   │   ├── earnings/
│   │   │   │   └── profile/
│   │   │   ├── guest/              # Guest-specific pages
│   │   │   │   ├── search/
│   │   │   │   ├── bookings/
│   │   │   │   └── profile/
│   │   │   └── layout.tsx          # Dashboard layout with navigation
│   │   ├── vehicles/               # Public vehicle pages
│   │   │   ├── [id]/
│   │   │   │   └── page.tsx        # Vehicle detail page
│   │   │   └── search/
│   │   │       └── page.tsx        # Vehicle search results
│   │   ├── api/                    # API routes (minimal - mostly for webhooks)
│   │   │   └── auth/
│   │   │       └── callback/
│   │   ├── globals.css             # Global styles and Tailwind imports
│   │   ├── layout.tsx              # Root layout component
│   │   ├── page.tsx                # Home page
│   │   ├── loading.tsx             # Global loading UI
│   │   ├── error.tsx               # Global error UI
│   │   └── not-found.tsx           # 404 page
│   ├── components/                 # Reusable UI components
│   │   ├── ui/                     # Base UI components
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   ├── modal.tsx
│   │   │   ├── card.tsx
│   │   │   ├── badge.tsx
│   │   │   ├── avatar.tsx
│   │   │   └── spinner.tsx
│   │   ├── forms/                  # Form-specific components
│   │   │   ├── vehicle-form.tsx
│   │   │   ├── booking-form.tsx
│   │   │   ├── login-form.tsx
│   │   │   └── search-form.tsx
│   │   ├── layout/                 # Layout components
│   │   │   ├── header.tsx
│   │   │   ├── footer.tsx
│   │   │   ├── sidebar.tsx
│   │   │   └── navigation.tsx
│   │   ├── features/               # Feature-specific components
│   │   │   ├── vehicles/
│   │   │   │   ├── vehicle-card.tsx
│   │   │   │   ├── vehicle-gallery.tsx
│   │   │   │   ├── vehicle-search.tsx
│   │   │   │   └── vehicle-map.tsx
│   │   │   ├── bookings/
│   │   │   │   ├── booking-card.tsx
│   │   │   │   ├── booking-status.tsx
│   │   │   │   └── booking-timeline.tsx
│   │   │   ├── payments/
│   │   │   │   ├── payment-form.tsx
│   │   │   │   └── payment-history.tsx
│   │   │   └── auth/
│   │   │       ├── auth-guard.tsx
│   │   │       └── role-guard.tsx
│   │   └── common/                 # Common reusable components
│   │       ├── loading-skeleton.tsx
│   │       ├── empty-state.tsx
│   │       ├── error-boundary.tsx
│   │       ├── confirm-dialog.tsx
│   │       └── toast-notification.tsx
│   ├── hooks/                      # Custom React hooks
│   │   ├── useAuth.ts              # Authentication hook
│   │   ├── useBookings.ts          # Booking management hook
│   │   ├── useVehicles.ts          # Vehicle operations hook
│   │   ├── useWebSocket.ts         # Real-time updates hook
│   │   ├── useLocalStorage.ts      # Local storage management
│   │   ├── useDebounce.ts          # Debounced search hook
│   │   └── useGeolocation.ts       # Location services hook
│   ├── lib/                        # Utility libraries and configurations
│   │   ├── api/                    # API client setup
│   │   │   ├── client.ts           # Axios configuration
│   │   │   ├── auth.ts             # Auth API calls
│   │   │   ├── vehicles.ts         # Vehicle API calls
│   │   │   ├── bookings.ts         # Booking API calls
│   │   │   ├── payments.ts         # Payment API calls
│   │   │   └── types.ts            # API response types
│   │   ├── external/               # External service integrations
│   │   │   ├── kakao-map.ts        # Kakao Map SDK wrapper
│   │   │   ├── toss-payments.ts    # Toss Payments SDK wrapper
│   │   │   └── websocket.ts        # WebSocket client
│   │   ├── utils/                  # Utility functions
│   │   │   ├── format.ts           # Date, currency, number formatting
│   │   │   ├── validation.ts       # Form validation rules
│   │   │   ├── storage.ts          # Local/session storage helpers
│   │   │   ├── auth.ts             # JWT token management
│   │   │   └── constants.ts        # App constants
│   │   ├── stores/                 # Zustand stores
│   │   │   ├── auth-store.ts       # Authentication state
│   │   │   ├── booking-store.ts    # Booking state management
│   │   │   ├── vehicle-store.ts    # Vehicle search and filters
│   │   │   ├── notification-store.ts # Real-time notifications
│   │   │   └── ui-store.ts         # UI state (modals, sidebars)
│   │   └── config/
│   │       ├── env.ts              # Environment variables
│   │       ├── routes.ts           # Route constants
│   │       └── theme.ts            # Tailwind theme extensions
│   ├── styles/                     # Additional stylesheets
│   │   ├── components.css          # Component-specific styles
│   │   ├── utilities.css           # Custom Tailwind utilities
│   │   └── animations.css          # Custom animations
│   └── types/                      # TypeScript type definitions
│       ├── auth.ts                 # Authentication types
│       ├── vehicle.ts              # Vehicle-related types
│       ├── booking.ts              # Booking-related types
│       ├── user.ts                 # User profile types
│       ├── payment.ts              # Payment types
│       ├── api.ts                  # API response types
│       └── common.ts               # Shared utility types
├── tests/                          # Test files
│   ├── __mocks__/                  # Mock implementations
│   │   ├── next-router.ts
│   │   ├── kakao-map.ts
│   │   └── websocket.ts
│   ├── components/                 # Component tests
│   ├── hooks/                      # Hook tests
│   ├── pages/                      # Page tests
│   ├── utils/                      # Utility function tests
│   └── setup.ts                    # Test environment setup
├── .env.local.example              # Environment variables template
├── .env.local                      # Local environment variables
├── next.config.js                  # Next.js configuration
├── tailwind.config.js              # Tailwind CSS configuration
├── tsconfig.json                   # TypeScript configuration
├── jest.config.js                  # Jest testing configuration
├── package.json                    # Dependencies and scripts
└── README.md                       # Project documentation
```

## Component Standards

### Component Template

```typescript
'use client'

import { FC, ReactNode } from 'react'
import { cn } from '@/lib/utils'

interface ComponentNameProps {
  children?: ReactNode
  className?: string
  variant?: 'primary' | 'secondary' | 'outline'
  size?: 'sm' | 'md' | 'lg'
  disabled?: boolean
  onClick?: () => void
}

export const ComponentName: FC<ComponentNameProps> = ({
  children,
  className,
  variant = 'primary',
  size = 'md',
  disabled = false,
  onClick,
  ...props
}) => {
  const baseClasses = 'inline-flex items-center justify-center rounded-md font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50'

  const variantClasses = {
    primary: 'bg-primary text-primary-foreground hover:bg-primary/90',
    secondary: 'bg-secondary text-secondary-foreground hover:bg-secondary/80',
    outline: 'border border-input bg-background hover:bg-accent hover:text-accent-foreground',
  }

  const sizeClasses = {
    sm: 'h-9 px-3 text-sm',
    md: 'h-10 px-4 py-2',
    lg: 'h-11 px-8 text-lg',
  }

  return (
    <button
      className={cn(
        baseClasses,
        variantClasses[variant],
        sizeClasses[size],
        className
      )}
      disabled={disabled}
      onClick={onClick}
      {...props}
    >
      {children}
    </button>
  )
}

export default ComponentName
```

### Naming Conventions

**Files and Components**:
- Components: PascalCase (`VehicleCard.tsx`)
- Hooks: camelCase with 'use' prefix (`useAuth.ts`)
- Utilities: camelCase (`formatCurrency.ts`)
- Pages: kebab-case directories (`vehicle-detail/page.tsx`)
- Types: PascalCase interfaces (`VehicleType`, `BookingStatus`)

**Component Organization**:
- One component per file
- Export default and named export
- Props interface same name as component + 'Props'
- Use TypeScript strict mode
- Implement proper error boundaries

## State Management

### Store Structure

```
stores/
├── auth-store.ts                   # User authentication and profile
├── vehicle-store.ts                # Vehicle search, filters, and details
├── booking-store.ts                # Booking lifecycle and history
├── notification-store.ts           # Real-time notifications and alerts
├── ui-store.ts                     # UI state (modals, sidebars, loading)
└── index.ts                        # Store exports and combinations
```

### State Management Template

```typescript
import { create } from 'zustand'
import { persist, createJSONStorage } from 'zustand/middleware'
import { immer } from 'zustand/middleware/immer'

interface AuthState {
  // State
  user: User | null
  token: string | null
  isAuthenticated: boolean
  isLoading: boolean
  error: string | null

  // Actions
  login: (credentials: LoginCredentials) => Promise<void>
  logout: () => void
  refreshToken: () => Promise<void>
  updateProfile: (data: Partial<User>) => Promise<void>
  clearError: () => void
  setLoading: (loading: boolean) => void
}

export const useAuthStore = create<AuthState>()(
  persist(
    immer((set, get) => ({
      // Initial state
      user: null,
      token: null,
      isAuthenticated: false,
      isLoading: false,
      error: null,

      // Actions
      login: async (credentials) => {
        set((state) => {
          state.isLoading = true
          state.error = null
        })

        try {
          const response = await authApi.login(credentials)
          set((state) => {
            state.user = response.user
            state.token = response.token
            state.isAuthenticated = true
            state.isLoading = false
          })
        } catch (error) {
          set((state) => {
            state.error = error.message
            state.isLoading = false
          })
        }
      },

      logout: () => {
        set((state) => {
          state.user = null
          state.token = null
          state.isAuthenticated = false
          state.error = null
        })
      },

      refreshToken: async () => {
        const currentToken = get().token
        if (!currentToken) return

        try {
          const response = await authApi.refreshToken(currentToken)
          set((state) => {
            state.token = response.token
            state.user = response.user
          })
        } catch (error) {
          // Force logout on refresh failure
          get().logout()
        }
      },

      updateProfile: async (data) => {
        set((state) => {
          state.isLoading = true
        })

        try {
          const updatedUser = await authApi.updateProfile(data)
          set((state) => {
            state.user = updatedUser
            state.isLoading = false
          })
        } catch (error) {
          set((state) => {
            state.error = error.message
            state.isLoading = false
          })
        }
      },

      clearError: () => {
        set((state) => {
          state.error = null
        })
      },

      setLoading: (loading) => {
        set((state) => {
          state.isLoading = loading
        })
      },
    })),
    {
      name: 'auth-store',
      storage: createJSONStorage(() => localStorage),
      partialize: (state) => ({
        user: state.user,
        token: state.token,
        isAuthenticated: state.isAuthenticated
      }),
    }
  )
)
```

## API Integration

### Service Template

```typescript
import { apiClient } from './client'
import type {
  Vehicle,
  VehicleSearchParams,
  VehicleCreateData,
  ApiResponse,
  PaginatedResponse
} from '../types'

class VehicleService {
  private readonly baseUrl = '/vehicles'

  async searchVehicles(params: VehicleSearchParams): Promise<PaginatedResponse<Vehicle>> {
    const response = await apiClient.get<PaginatedResponse<Vehicle>>(
      `${this.baseUrl}/search`,
      { params }
    )
    return response.data
  }

  async getVehicleDetails(id: string): Promise<Vehicle> {
    const response = await apiClient.get<ApiResponse<Vehicle>>(
      `${this.baseUrl}/${id}`
    )
    return response.data.data
  }

  async createVehicle(data: VehicleCreateData): Promise<Vehicle> {
    const response = await apiClient.post<ApiResponse<Vehicle>>(
      this.baseUrl,
      data
    )
    return response.data.data
  }

  async updateVehicle(id: string, data: Partial<VehicleCreateData>): Promise<Vehicle> {
    const response = await apiClient.put<ApiResponse<Vehicle>>(
      `${this.baseUrl}/${id}`,
      data
    )
    return response.data.data
  }

  async deleteVehicle(id: string): Promise<void> {
    await apiClient.delete(`${this.baseUrl}/${id}`)
  }

  async uploadVehicleImages(id: string, files: File[]): Promise<string[]> {
    const formData = new FormData()
    files.forEach((file, index) => {
      formData.append(`images`, file)
    })

    const response = await apiClient.post<ApiResponse<{ urls: string[] }>>(
      `${this.baseUrl}/${id}/images`,
      formData,
      {
        headers: {
          'Content-Type': 'multipart/form-data',
        },
      }
    )
    return response.data.data.urls
  }
}

export const vehicleService = new VehicleService()
```

### API Client Configuration

```typescript
import axios, { AxiosResponse, AxiosError } from 'axios'
import { useAuthStore } from '@/lib/stores/auth-store'
import { toast } from '@/components/ui/toast'

// Create axios instance
export const apiClient = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL || 'http://localhost:3001/api/v1',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
})

// Request interceptor for auth tokens
apiClient.interceptors.request.use(
  (config) => {
    const token = useAuthStore.getState().token
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  (error) => {
    return Promise.reject(error)
  }
)

// Response interceptor for error handling
apiClient.interceptors.response.use(
  (response: AxiosResponse) => {
    return response
  },
  async (error: AxiosError) => {
    const originalRequest = error.config as any

    // Handle 401 errors - token refresh
    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true

      try {
        await useAuthStore.getState().refreshToken()
        const newToken = useAuthStore.getState().token
        if (newToken) {
          originalRequest.headers.Authorization = `Bearer ${newToken}`
          return apiClient(originalRequest)
        }
      } catch (refreshError) {
        useAuthStore.getState().logout()
        window.location.href = '/login'
        return Promise.reject(refreshError)
      }
    }

    // Handle other errors
    const message = error.response?.data?.error?.message || '오류가 발생했습니다.'

    if (error.response?.status >= 500) {
      toast.error('서버 오류가 발생했습니다. 잠시 후 다시 시도해주세요.')
    } else if (error.response?.status === 403) {
      toast.error('접근 권한이 없습니다.')
    } else {
      toast.error(message)
    }

    return Promise.reject(error)
  }
)

export default apiClient
```

## Routing

### Route Configuration

```typescript
// lib/config/routes.ts
export const routes = {
  // Public routes
  home: '/',
  login: '/login',
  register: '/register',

  // Vehicle routes
  vehicleSearch: '/vehicles/search',
  vehicleDetail: (id: string) => `/vehicles/${id}`,

  // Guest routes
  guest: {
    dashboard: '/guest',
    bookings: '/guest/bookings',
    profile: '/guest/profile',
  },

  // Host routes
  host: {
    dashboard: '/host',
    vehicles: '/host/vehicles',
    vehicleCreate: '/host/vehicles/create',
    vehicleEdit: (id: string) => `/host/vehicles/${id}/edit`,
    bookings: '/host/bookings',
    earnings: '/host/earnings',
    profile: '/host/profile',
  },

  // Admin routes
  admin: {
    dashboard: '/admin',
    users: '/admin/users',
    vehicles: '/admin/vehicles',
    bookings: '/admin/bookings',
    reports: '/admin/reports',
  },
} as const

// Protected route configuration with role-based access
export const protectedRoutes = {
  '/guest': ['guest'],
  '/host': ['host'],
  '/admin': ['admin'],
} as const

export const publicRoutes = [
  '/',
  '/login',
  '/register',
  '/vehicles/search',
  '/vehicles/[id]',
] as const
```

### Protected Route Pattern

```typescript
// components/features/auth/auth-guard.tsx
'use client'

import { useEffect } from 'react'
import { useRouter } from 'next/navigation'
import { useAuthStore } from '@/lib/stores/auth-store'
import { routes } from '@/lib/config/routes'
import { Spinner } from '@/components/ui/spinner'

interface AuthGuardProps {
  children: React.ReactNode
  requireAuth?: boolean
  requiredRoles?: string[]
  fallbackPath?: string
}

export const AuthGuard: React.FC<AuthGuardProps> = ({
  children,
  requireAuth = true,
  requiredRoles = [],
  fallbackPath = routes.login,
}) => {
  const router = useRouter()
  const { isAuthenticated, user, isLoading } = useAuthStore()

  useEffect(() => {
    if (isLoading) return

    // Check authentication requirement
    if (requireAuth && !isAuthenticated) {
      router.push(fallbackPath)
      return
    }

    // Check role requirements
    if (requiredRoles.length > 0 && user) {
      const hasRequiredRole = requiredRoles.includes(user.role)
      if (!hasRequiredRole) {
        // Redirect based on user role
        const redirectPath = user.role === 'host' ? routes.host.dashboard : routes.guest.dashboard
        router.push(redirectPath)
        return
      }
    }
  }, [isAuthenticated, user, isLoading, requireAuth, requiredRoles, router, fallbackPath])

  if (isLoading) {
    return (
      <div className="flex items-center justify-center min-h-screen">
        <Spinner size="lg" />
      </div>
    )
  }

  if (requireAuth && !isAuthenticated) {
    return null
  }

  if (requiredRoles.length > 0 && user && !requiredRoles.includes(user.role)) {
    return null
  }

  return <>{children}</>
}
```

## Styling Guidelines

### Styling Approach

**Primary Approach**: Tailwind CSS utility-first methodology
- **Component Composition**: Build components using Tailwind utilities
- **Custom Components**: Use `@apply` directive sparingly for complex patterns
- **Responsive Design**: Mobile-first breakpoint system
- **Design System**: Consistent spacing, typography, and color scales
- **Korean Typography**: Optimized for Korean text rendering and reading patterns

**Component Styling Pattern**:
```typescript
// Prefer utility classes for flexibility
<button className="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 transition-colors" />

// Use clsx/cn for conditional classes
import { cn } from '@/lib/utils'

const Button = ({ variant, className, ...props }) => (
  <button
    className={cn(
      'px-4 py-2 rounded-md transition-colors',
      variant === 'primary' && 'bg-blue-600 text-white hover:bg-blue-700',
      variant === 'secondary' && 'bg-gray-200 text-gray-900 hover:bg-gray-300',
      className
    )}
    {...props}
  />
)
```

### Global Theme Variables

```css
/* styles/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    /* Primary Brand Colors - Korean Car Sharing Theme */
    --color-primary-50: #eff6ff;
    --color-primary-100: #dbeafe;
    --color-primary-200: #bfdbfe;
    --color-primary-300: #93c5fd;
    --color-primary-400: #60a5fa;
    --color-primary-500: #2563eb;  /* Main brand blue */
    --color-primary-600: #1d4ed8;
    --color-primary-700: #1e40af;
    --color-primary-800: #1e3a8a;
    --color-primary-900: #1e3a8a;

    /* Success/Status Colors */
    --color-success-50: #f0fdf4;
    --color-success-500: #22c55e;  /* Booking confirmed */
    --color-success-600: #16a34a;

    /* Warning Colors */
    --color-warning-50: #fffbeb;
    --color-warning-500: #f59e0b;  /* Booking pending */
    --color-warning-600: #d97706;

    /* Error/Danger Colors */
    --color-error-50: #fef2f2;
    --color-error-500: #ef4444;    /* Booking rejected */
    --color-error-600: #dc2626;

    /* Neutral Grays */
    --color-gray-50: #f9fafb;
    --color-gray-100: #f3f4f6;
    --color-gray-200: #e5e7eb;
    --color-gray-300: #d1d5db;
    --color-gray-400: #9ca3af;
    --color-gray-500: #6b7280;
    --color-gray-600: #4b5563;
    --color-gray-700: #374151;
    --color-gray-800: #1f2937;
    --color-gray-900: #111827;

    /* Typography Scale for Korean Text */
    --font-size-xs: 0.75rem;      /* 12px - captions */
    --font-size-sm: 0.875rem;     /* 14px - body small */
    --font-size-base: 1rem;       /* 16px - body */
    --font-size-lg: 1.125rem;     /* 18px - body large */
    --font-size-xl: 1.25rem;      /* 20px - headings */
    --font-size-2xl: 1.5rem;      /* 24px - page titles */
    --font-size-3xl: 1.875rem;    /* 30px - hero text */

    /* Spacing Scale (8px base) */
    --spacing-1: 0.25rem;         /* 4px */
    --spacing-2: 0.5rem;          /* 8px */
    --spacing-3: 0.75rem;         /* 12px */
    --spacing-4: 1rem;            /* 16px */
    --spacing-5: 1.25rem;         /* 20px */
    --spacing-6: 1.5rem;          /* 24px */
    --spacing-8: 2rem;            /* 32px */
    --spacing-10: 2.5rem;         /* 40px */
    --spacing-12: 3rem;           /* 48px */
    --spacing-16: 4rem;           /* 64px */

    /* Border Radius */
    --radius-sm: 0.25rem;         /* 4px */
    --radius-md: 0.375rem;        /* 6px */
    --radius-lg: 0.5rem;          /* 8px */
    --radius-xl: 0.75rem;         /* 12px */
    --radius-2xl: 1rem;           /* 16px */

    /* Shadows */
    --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
    --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
    --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
    --shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1);

    /* Z-index Scale */
    --z-dropdown: 1000;
    --z-sticky: 1020;
    --z-fixed: 1030;
    --z-modal-backdrop: 1040;
    --z-modal: 1050;
    --z-popover: 1060;
    --z-tooltip: 1070;
    --z-toast: 1080;
  }

  /* Dark mode variables */
  .dark {
    --color-primary-50: #1e3a8a;
    --color-primary-900: #eff6ff;

    --color-gray-50: #111827;
    --color-gray-100: #1f2937;
    --color-gray-200: #374151;
    --color-gray-800: #f3f4f6;
    --color-gray-900: #f9fafb;
  }

  /* Korean-optimized typography */
  body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Malgun Gothic', 'Apple SD Gothic Neo', 'Noto Sans KR', sans-serif;
    font-feature-settings: 'kern' 1;
    text-rendering: optimizeLegibility;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  /* Korean text specific styles */
  .korean-text {
    word-break: keep-all;
    line-height: 1.6;
  }

  /* Focus states for accessibility */
  .focus-visible {
    @apply outline-none ring-2 ring-primary-500 ring-offset-2 ring-offset-white;
  }
}

@layer components {
  /* Button variants */
  .btn-primary {
    @apply px-4 py-2 bg-primary-600 text-white rounded-md hover:bg-primary-700 focus:outline-none focus:ring-2 focus:ring-primary-500 focus:ring-offset-2 transition-colors;
  }

  .btn-secondary {
    @apply px-4 py-2 bg-gray-200 text-gray-900 rounded-md hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2 transition-colors;
  }

  /* Card component */
  .card {
    @apply bg-white rounded-lg shadow-md border border-gray-200 overflow-hidden;
  }

  .card-header {
    @apply px-6 py-4 border-b border-gray-200;
  }

  .card-body {
    @apply px-6 py-4;
  }

  /* Input styles */
  .input {
    @apply block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-primary-500 focus:border-primary-500;
  }

  .input-error {
    @apply border-red-300 focus:ring-red-500 focus:border-red-500;
  }

  /* Korean mobile optimizations */
  .mobile-input {
    @apply text-base; /* Prevents zoom on iOS */
  }

  /* Touch-friendly spacing for mobile */
  .touch-target {
    @apply min-h-[44px] min-w-[44px];
  }
}

@layer utilities {
  /* Korean text utilities */
  .break-keep-all {
    word-break: keep-all;
  }

  .text-korean {
    font-family: 'Malgun Gothic', 'Apple SD Gothic Neo', 'Noto Sans KR', sans-serif;
    word-break: keep-all;
    line-height: 1.6;
  }

  /* Animation utilities */
  .animate-fade-in {
    animation: fadeIn 0.3s ease-in-out;
  }

  .animate-slide-up {
    animation: slideUp 0.3s ease-out;
  }

  @keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
  }

  @keyframes slideUp {
    from {
      transform: translateY(20px);
      opacity: 0;
    }
    to {
      transform: translateY(0);
      opacity: 1;
    }
  }

  /* Responsive utilities for Korean mobile */
  .mobile-container {
    @apply max-w-sm mx-auto px-4;
  }

  .desktop-container {
    @apply max-w-7xl mx-auto px-4 sm:px-6 lg:px-8;
  }
}
```

## Testing Requirements

### Component Test Template

```typescript
// tests/components/ui/button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import { describe, it, expect, vi } from 'vitest'
import { Button } from '@/components/ui/button'

describe('Button Component', () => {
  it('renders with correct text', () => {
    render(<Button>클릭하세요</Button>)
    expect(screen.getByRole('button', { name: '클릭하세요' })).toBeInTheDocument()
  })

  it('calls onClick handler when clicked', () => {
    const handleClick = vi.fn()
    render(<Button onClick={handleClick}>클릭</Button>)

    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })

  it('applies correct variant classes', () => {
    render(<Button variant="secondary">버튼</Button>)
    const button = screen.getByRole('button')

    expect(button).toHaveClass('bg-secondary')
    expect(button).not.toHaveClass('bg-primary')
  })

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>비활성화</Button>)
    const button = screen.getByRole('button')

    expect(button).toBeDisabled()
    expect(button).toHaveClass('disabled:opacity-50')
  })

  it('handles keyboard navigation', () => {
    const handleClick = vi.fn()
    render(<Button onClick={handleClick}>키보드 테스트</Button>)

    const button = screen.getByRole('button')
    button.focus()
    fireEvent.keyDown(button, { key: 'Enter', code: 'Enter' })

    expect(handleClick).toHaveBeenCalled()
  })
})
```

### Testing Best Practices

1. **Unit Tests**: Test individual components in isolation
2. **Integration Tests**: Test component interactions and data flow
3. **E2E Tests**: Test critical user flows (login, booking, payment)
4. **Coverage Goals**: Aim for 80% code coverage
5. **Test Structure**: Arrange-Act-Assert pattern
6. **Mock External Dependencies**: API calls, routing, external services

**Mock Strategy**:
- Mock API calls using MSW (Mock Service Worker)
- Mock Kakao Map SDK for location tests
- Mock Toss Payments for payment flow tests
- Mock WebSocket connections for real-time features

## Environment Configuration

```bash
# Frontend Environment Variables (.env.local)

# API Configuration
NEXT_PUBLIC_API_URL=http://localhost:3001/api/v1
NEXT_PUBLIC_WS_URL=ws://localhost:3001

# Korean Service APIs
NEXT_PUBLIC_KAKAO_MAP_API_KEY=your_kakao_map_api_key
NEXT_PUBLIC_TOSS_PAYMENTS_CLIENT_KEY=your_toss_payments_client_key

# Feature Flags
NEXT_PUBLIC_ENABLE_REAL_TIME=true
NEXT_PUBLIC_ENABLE_DARK_MODE=true
NEXT_PUBLIC_ENABLE_PWA=true

# Analytics and Monitoring
NEXT_PUBLIC_GA_TRACKING_ID=your_google_analytics_id
NEXT_PUBLIC_SENTRY_DSN=your_sentry_dsn

# Development
NEXT_PUBLIC_ENV=development
NEXT_PUBLIC_DEBUG=true

# Image Upload
NEXT_PUBLIC_MAX_FILE_SIZE=5242880  # 5MB
NEXT_PUBLIC_ALLOWED_FILE_TYPES=image/jpeg,image/png,image/webp

# Geolocation
NEXT_PUBLIC_DEFAULT_LAT=37.5665  # Seoul coordinates
NEXT_PUBLIC_DEFAULT_LNG=126.9780
NEXT_PUBLIC_SEARCH_RADIUS=10     # kilometers
```

## Frontend Developer Standards

### Critical Coding Rules

1. **TypeScript Strict Mode**: All components must use TypeScript with strict mode enabled
2. **Props Validation**: Define proper TypeScript interfaces for all component props
3. **State Management**: Use Zustand stores for complex state, useState for local component state only
4. **API Calls**: Never make direct fetch/axios calls in components - use service layer and hooks
5. **Error Boundaries**: Wrap async operations in try-catch blocks and handle errors gracefully
6. **Accessibility**: All interactive elements must have proper ARIA labels and keyboard navigation
7. **Performance**: Use React.memo, useMemo, and useCallback appropriately to prevent unnecessary re-renders
8. **Responsive Design**: All components must work on mobile devices (320px minimum width)
9. **Korean Text**: Use proper Korean typography settings and word-break rules
10. **Loading States**: Always show loading indicators for async operations
11. **Error States**: Provide clear error messages in Korean with retry options
12. **Real-time Updates**: Use WebSocket hooks for live data, not polling

### Quick Reference

**Common Commands**:
```bash
# Development server
npm run dev

# Build for production
npm run build

# Run tests
npm test
npm run test:watch
npm run test:coverage

# Type checking
npm run type-check

# Linting and formatting
npm run lint
npm run lint:fix
npm run format
```

**Key Import Patterns**:
```typescript
// UI Components
import { Button } from '@/components/ui/button'
import { Card } from '@/components/ui/card'

// Feature Components
import { VehicleCard } from '@/components/features/vehicles/vehicle-card'

// Hooks
import { useAuth } from '@/hooks/useAuth'
import { useBookings } from '@/hooks/useBookings'

// Stores
import { useAuthStore } from '@/lib/stores/auth-store'

// Utils
import { cn } from '@/lib/utils'
import { formatCurrency } from '@/lib/utils/format'

// Types
import type { Vehicle, Booking, User } from '@/types'
```

**File Naming Conventions**:
- Components: `PascalCase.tsx` (e.g., `VehicleCard.tsx`)
- Hooks: `camelCase.ts` (e.g., `useVehicleSearch.ts`)
- Utils: `camelCase.ts` (e.g., `formatCurrency.ts`)
- Pages: `page.tsx` (Next.js App Router)
- Layouts: `layout.tsx` (Next.js App Router)

**Project-Specific Patterns**:
- Always use `'use client'` directive for interactive components
- Implement proper loading skeletons for better UX
- Use Framer Motion for smooth transitions and animations
- Implement proper image optimization with Next.js Image component
- Use proper Korean localization patterns for text and formatting
- Implement proper error boundaries for robust error handling