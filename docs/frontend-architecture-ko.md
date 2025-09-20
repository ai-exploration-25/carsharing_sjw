# 카셰어링 서비스 Frontend Architecture Document

## 템플릿 및 프레임워크 선택

### 프레임워크 결정

**선택된 프레임워크**: React 18 및 TypeScript와 함께 Next.js 14
- **근거**: SEO를 위한 서버사이드 렌더링, 뛰어난 성능, 견고한 생태계
- **시작 템플릿**: 없음 - 최대 제어를 위한 커스텀 구현
- **UI 프레임워크**: 한국 시장 최적화를 위한 Tailwind CSS + Headless UI

**요구사항 분석**:
- **모바일 우선 반응형 디자인** (한국 사용자의 80% 모바일 사용)
- **하이브리드 B2B/B2C 인터페이스** 다양한 UX 패턴 요구
- **실시간 기능** 예약 알림 및 상태 업데이트용
- **한국 서비스 통합** (카카오맵, 토스페이먼츠)

### 변경 이력

| 날짜 | 버전 | 설명 | 작성자 |
|------|---------|-------------|---------|
| 2025-09-20 | v1.0 | 초기 frontend 아키텍처 생성 | Frontend Architect |

## Frontend 기술 스택

### 기술 스택 테이블

| 카테고리 | 기술 | 버전 | 목적 | 근거 |
|----------|------------|---------|---------|-----------|
| 프레임워크 | Next.js | 14.0.4 | SSR/SSG를 지원하는 React 메타프레임워크 | 뛰어난 성능, SEO 이점, 한국 시장에서 검증됨 |
| UI 라이브러리 | React | 18.2.0 | 컴포넌트 기반 UI 라이브러리 | 업계 표준, 뛰어난 생태계, 팀 전문성 |
| 상태 관리 | Zustand | 4.4.7 | 경량 상태 관리 | 간단한 API, TypeScript 지원, MVP에 Redux보다 나음 |
| 라우팅 | Next.js Router | 14.0.4 | 파일 기반 라우팅 시스템 | 내장, app 디렉토리 지원, 뛰어난 DX |
| 빌드 도구 | Next.js | 14.0.4 | 통합 빌드 시스템 | 무설정, React에 최적화, 뛰어난 성능 |
| 스타일링 | Tailwind CSS | 3.3.6 | 유틸리티 우선 CSS 프레임워크 | 빠른 개발, 일관된 디자인 시스템, 모바일 우선 |
| 테스팅 | Jest + Testing Library | 29.7.0 + 14.1.2 | 컴포넌트 및 단위 테스팅 | React 중심 테스팅, 뛰어난 모킹 기능 |
| 컴포넌트 라이브러리 | Headless UI | 1.7.17 | 스타일이 없는 접근 가능한 컴포넌트 | 내장 접근성, Tailwind 호환, 유연함 |
| 폼 처리 | React Hook Form | 7.48.2 | 성능 중심 폼 관리 | 최소 재렌더링, 뛰어난 검증, TypeScript 지원 |
| 애니메이션 | Framer Motion | 10.16.16 | React용 모션 라이브러리 | 부드러운 애니메이션, 제스처 지원, 뛰어난 성능 |
| 개발 도구 | ESLint + Prettier | 8.55.0 + 3.1.1 | 코드 품질 및 포맷팅 | 일관된 코드 스타일, 오류 방지, 팀 협업 |

## 프로젝트 구조

```
frontend/
├── public/                         # 정적 자산
│   ├── images/
│   │   ├── cars/                   # 차량 플레이스홀더 이미지
│   │   ├── icons/                  # 커스텀 아이콘 및 로고
│   │   └── avatars/                # 기본 프로필 이미지
│   ├── favicon/                    # 파비콘 변형
│   └── manifest.json               # PWA 매니페스트
├── src/
│   ├── app/                        # Next.js 14 app 디렉토리
│   │   ├── (auth)/                 # 인증 페이지를 위한 라우트 그룹
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   ├── register/
│   │   │   │   └── page.tsx
│   │   │   └── layout.tsx          # 인증 레이아웃 래퍼
│   │   ├── (dashboard)/            # 보호된 대시보드 라우트
│   │   │   ├── host/               # 호스트 전용 페이지
│   │   │   │   ├── vehicles/
│   │   │   │   ├── bookings/
│   │   │   │   ├── earnings/
│   │   │   │   └── profile/
│   │   │   ├── guest/              # 게스트 전용 페이지
│   │   │   │   ├── search/
│   │   │   │   ├── bookings/
│   │   │   │   └── profile/
│   │   │   └── layout.tsx          # 네비게이션을 포함한 대시보드 레이아웃
│   │   ├── vehicles/               # 공개 차량 페이지
│   │   │   ├── [id]/
│   │   │   │   └── page.tsx        # 차량 상세 페이지
│   │   │   └── search/
│   │   │       └── page.tsx        # 차량 검색 결과
│   │   ├── api/                    # API 라우트 (최소 - 주로 웹훅용)
│   │   │   └── auth/
│   │   │       └── callback/
│   │   ├── globals.css             # 글로벌 스타일 및 Tailwind imports
│   │   ├── layout.tsx              # 루트 레이아웃 컴포넌트
│   │   ├── page.tsx                # 홈 페이지
│   │   ├── loading.tsx             # 글로벌 로딩 UI
│   │   ├── error.tsx               # 글로벌 에러 UI
│   │   └── not-found.tsx           # 404 페이지
│   ├── components/                 # 재사용 가능한 UI 컴포넌트
│   │   ├── ui/                     # 기본 UI 컴포넌트
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   ├── modal.tsx
│   │   │   ├── card.tsx
│   │   │   ├── badge.tsx
│   │   │   ├── avatar.tsx
│   │   │   └── spinner.tsx
│   │   ├── forms/                  # 폼 전용 컴포넌트
│   │   │   ├── vehicle-form.tsx
│   │   │   ├── booking-form.tsx
│   │   │   ├── login-form.tsx
│   │   │   └── search-form.tsx
│   │   ├── layout/                 # 레이아웃 컴포넌트
│   │   │   ├── header.tsx
│   │   │   ├── footer.tsx
│   │   │   ├── sidebar.tsx
│   │   │   └── navigation.tsx
│   │   ├── features/               # 기능별 컴포넌트
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
│   │   └── common/                 # 공통 재사용 컴포넌트
│   │       ├── loading-skeleton.tsx
│   │       ├── empty-state.tsx
│   │       ├── error-boundary.tsx
│   │       ├── confirm-dialog.tsx
│   │       └── toast-notification.tsx
│   ├── hooks/                      # 커스텀 React 훅
│   │   ├── useAuth.ts              # 인증 훅
│   │   ├── useBookings.ts          # 예약 관리 훅
│   │   ├── useVehicles.ts          # 차량 작업 훅
│   │   ├── useWebSocket.ts         # 실시간 업데이트 훅
│   │   ├── useLocalStorage.ts      # 로컬 저장소 관리
│   │   ├── useDebounce.ts          # 디바운스된 검색 훅
│   │   └── useGeolocation.ts       # 위치 서비스 훅
│   ├── lib/                        # 유틸리티 라이브러리 및 설정
│   │   ├── api/                    # API 클라이언트 설정
│   │   │   ├── client.ts           # Axios 설정
│   │   │   ├── auth.ts             # Auth API 호출
│   │   │   ├── vehicles.ts         # Vehicle API 호출
│   │   │   ├── bookings.ts         # Booking API 호출
│   │   │   ├── payments.ts         # Payment API 호출
│   │   │   └── types.ts            # API 응답 타입
│   │   ├── external/               # 외부 서비스 통합
│   │   │   ├── kakao-map.ts        # 카카오맵 SDK 래퍼
│   │   │   ├── toss-payments.ts    # 토스페이먼츠 SDK 래퍼
│   │   │   └── websocket.ts        # WebSocket 클라이언트
│   │   ├── utils/                  # 유틸리티 함수
│   │   │   ├── format.ts           # 날짜, 통화, 숫자 포맷팅
│   │   │   ├── validation.ts       # 폼 검증 규칙
│   │   │   ├── storage.ts          # 로컬/세션 저장소 헬퍼
│   │   │   ├── auth.ts             # JWT 토큰 관리
│   │   │   └── constants.ts        # 앱 상수
│   │   ├── stores/                 # Zustand 스토어
│   │   │   ├── auth-store.ts       # 인증 상태
│   │   │   ├── booking-store.ts    # 예약 상태 관리
│   │   │   ├── vehicle-store.ts    # 차량 검색 및 필터
│   │   │   ├── notification-store.ts # 실시간 알림
│   │   │   └── ui-store.ts         # UI 상태 (모달, 사이드바)
│   │   └── config/
│   │       ├── env.ts              # 환경 변수
│   │       ├── routes.ts           # 라우트 상수
│   │       └── theme.ts            # Tailwind 테마 확장
│   ├── styles/                     # 추가 스타일시트
│   │   ├── components.css          # 컴포넌트별 스타일
│   │   ├── utilities.css           # 커스텀 Tailwind 유틸리티
│   │   └── animations.css          # 커스텀 애니메이션
│   └── types/                      # TypeScript 타입 정의
│       ├── auth.ts                 # 인증 타입
│       ├── vehicle.ts              # 차량 관련 타입
│       ├── booking.ts              # 예약 관련 타입
│       ├── user.ts                 # 사용자 프로필 타입
│       ├── payment.ts              # 결제 타입
│       ├── api.ts                  # API 응답 타입
│       └── common.ts               # 공유 유틸리티 타입
├── tests/                          # 테스트 파일
│   ├── __mocks__/                  # 모킹 구현
│   │   ├── next-router.ts
│   │   ├── kakao-map.ts
│   │   └── websocket.ts
│   ├── components/                 # 컴포넌트 테스트
│   ├── hooks/                      # 훅 테스트
│   ├── pages/                      # 페이지 테스트
│   ├── utils/                      # 유틸리티 함수 테스트
│   └── setup.ts                    # 테스트 환경 설정
├── .env.local.example              # 환경 변수 템플릿
├── .env.local                      # 로컬 환경 변수
├── next.config.js                  # Next.js 설정
├── tailwind.config.js              # Tailwind CSS 설정
├── tsconfig.json                   # TypeScript 설정
├── jest.config.js                  # Jest 테스팅 설정
├── package.json                    # 의존성 및 스크립트
└── README.md                       # 프로젝트 문서
```

## 컴포넌트 표준

### 컴포넌트 템플릿

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

### 명명 규칙

**파일 및 컴포넌트**:
- 컴포넌트: PascalCase (`VehicleCard.tsx`)
- 훅: 'use' 접두사와 camelCase (`useAuth.ts`)
- 유틸리티: camelCase (`formatCurrency.ts`)
- 페이지: kebab-case 디렉토리 (`vehicle-detail/page.tsx`)
- 타입: PascalCase 인터페이스 (`VehicleType`, `BookingStatus`)

**컴포넌트 조직**:
- 파일당 하나의 컴포넌트
- 기본 내보내기 및 명명된 내보내기
- Props 인터페이스는 컴포넌트 이름 + 'Props'
- TypeScript strict 모드 사용
- 적절한 에러 경계 구현

## 상태 관리

### 스토어 구조

```
stores/
├── auth-store.ts                   # 사용자 인증 및 프로필
├── vehicle-store.ts                # 차량 검색, 필터 및 상세정보
├── booking-store.ts                # 예약 라이프사이클 및 히스토리
├── notification-store.ts           # 실시간 알림 및 경고
├── ui-store.ts                     # UI 상태 (모달, 사이드바, 로딩)
└── index.ts                        # 스토어 내보내기 및 조합
```

### 상태 관리 템플릿

```typescript
import { create } from 'zustand'
import { persist, createJSONStorage } from 'zustand/middleware'
import { immer } from 'zustand/middleware/immer'

interface AuthState {
  // 상태
  user: User | null
  token: string | null
  isAuthenticated: boolean
  isLoading: boolean
  error: string | null

  // 액션
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
      // 초기 상태
      user: null,
      token: null,
      isAuthenticated: false,
      isLoading: false,
      error: null,

      // 액션
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
          // 갱신 실패 시 강제 로그아웃
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

## API 통합

### 서비스 템플릿

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

### API 클라이언트 설정

```typescript
import axios, { AxiosResponse, AxiosError } from 'axios'
import { useAuthStore } from '@/lib/stores/auth-store'
import { toast } from '@/components/ui/toast'

// Axios 인스턴스 생성
export const apiClient = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL || 'http://localhost:3001/api/v1',
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
})

// 인증 토큰을 위한 요청 인터셉터
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

// 에러 처리를 위한 응답 인터셉터
apiClient.interceptors.response.use(
  (response: AxiosResponse) => {
    return response
  },
  async (error: AxiosError) => {
    const originalRequest = error.config as any

    // 401 에러 처리 - 토큰 갱신
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

    // 기타 에러 처리
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

## 라우팅

### 라우트 설정

```typescript
// lib/config/routes.ts
export const routes = {
  // 공개 라우트
  home: '/',
  login: '/login',
  register: '/register',

  // 차량 라우트
  vehicleSearch: '/vehicles/search',
  vehicleDetail: (id: string) => `/vehicles/${id}`,

  // 게스트 라우트
  guest: {
    dashboard: '/guest',
    bookings: '/guest/bookings',
    profile: '/guest/profile',
  },

  // 호스트 라우트
  host: {
    dashboard: '/host',
    vehicles: '/host/vehicles',
    vehicleCreate: '/host/vehicles/create',
    vehicleEdit: (id: string) => `/host/vehicles/${id}/edit`,
    bookings: '/host/bookings',
    earnings: '/host/earnings',
    profile: '/host/profile',
  },

  // 관리자 라우트
  admin: {
    dashboard: '/admin',
    users: '/admin/users',
    vehicles: '/admin/vehicles',
    bookings: '/admin/bookings',
    reports: '/admin/reports',
  },
} as const

// 역할 기반 액세스가 있는 보호된 라우트 설정
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

### 보호된 라우트 패턴

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

    // 인증 요구사항 확인
    if (requireAuth && !isAuthenticated) {
      router.push(fallbackPath)
      return
    }

    // 역할 요구사항 확인
    if (requiredRoles.length > 0 && user) {
      const hasRequiredRole = requiredRoles.includes(user.role)
      if (!hasRequiredRole) {
        // 사용자 역할에 따라 리디렉션
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

## 스타일링 가이드라인

### 스타일링 접근법

**주요 접근법**: Tailwind CSS 유틸리티 우선 방법론
- **컴포넌트 구성**: Tailwind 유틸리티를 사용하여 컴포넌트 구축
- **커스텀 컴포넌트**: 복잡한 패턴을 위해 `@apply` 지시문을 제한적으로 사용
- **반응형 디자인**: 모바일 우선 브레이크포인트 시스템
- **디자인 시스템**: 일관된 간격, 타이포그래피 및 색상 스케일
- **한국 타이포그래피**: 한국어 텍스트 렌더링 및 읽기 패턴에 최적화

**컴포넌트 스타일링 패턴**:
```typescript
// 유연성을 위한 유틸리티 클래스 선호
<button className="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 transition-colors" />

// 조건부 클래스를 위한 clsx/cn 사용
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

### 글로벌 테마 변수

```css
/* styles/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    /* 주요 브랜드 색상 - 한국 카셰어링 테마 */
    --color-primary-50: #eff6ff;
    --color-primary-100: #dbeafe;
    --color-primary-200: #bfdbfe;
    --color-primary-300: #93c5fd;
    --color-primary-400: #60a5fa;
    --color-primary-500: #2563eb;  /* 메인 브랜드 블루 */
    --color-primary-600: #1d4ed8;
    --color-primary-700: #1e40af;
    --color-primary-800: #1e3a8a;
    --color-primary-900: #1e3a8a;

    /* 성공/상태 색상 */
    --color-success-50: #f0fdf4;
    --color-success-500: #22c55e;  /* 예약 확정됨 */
    --color-success-600: #16a34a;

    /* 경고 색상 */
    --color-warning-50: #fffbeb;
    --color-warning-500: #f59e0b;  /* 예약 대기 중 */
    --color-warning-600: #d97706;

    /* 오류/위험 색상 */
    --color-error-50: #fef2f2;
    --color-error-500: #ef4444;    /* 예약 거부됨 */
    --color-error-600: #dc2626;

    /* 중성 회색 */
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

    /* 한국어 텍스트를 위한 타이포그래피 스케일 */
    --font-size-xs: 0.75rem;      /* 12px - 캡션 */
    --font-size-sm: 0.875rem;     /* 14px - 작은 본문 */
    --font-size-base: 1rem;       /* 16px - 본문 */
    --font-size-lg: 1.125rem;     /* 18px - 큰 본문 */
    --font-size-xl: 1.25rem;      /* 20px - 제목 */
    --font-size-2xl: 1.5rem;      /* 24px - 페이지 제목 */
    --font-size-3xl: 1.875rem;    /* 30px - 히어로 텍스트 */

    /* 간격 스케일 (8px 기본) */
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

    /* 테두리 반경 */
    --radius-sm: 0.25rem;         /* 4px */
    --radius-md: 0.375rem;        /* 6px */
    --radius-lg: 0.5rem;          /* 8px */
    --radius-xl: 0.75rem;         /* 12px */
    --radius-2xl: 1rem;           /* 16px */

    /* 그림자 */
    --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
    --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
    --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
    --shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1);

    /* Z-index 스케일 */
    --z-dropdown: 1000;
    --z-sticky: 1020;
    --z-fixed: 1030;
    --z-modal-backdrop: 1040;
    --z-modal: 1050;
    --z-popover: 1060;
    --z-tooltip: 1070;
    --z-toast: 1080;
  }

  /* 다크 모드 변수 */
  .dark {
    --color-primary-50: #1e3a8a;
    --color-primary-900: #eff6ff;

    --color-gray-50: #111827;
    --color-gray-100: #1f2937;
    --color-gray-200: #374151;
    --color-gray-800: #f3f4f6;
    --color-gray-900: #f9fafb;
  }

  /* 한국어 최적화 타이포그래피 */
  body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Malgun Gothic', 'Apple SD Gothic Neo', 'Noto Sans KR', sans-serif;
    font-feature-settings: 'kern' 1;
    text-rendering: optimizeLegibility;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  /* 한국어 텍스트 특정 스타일 */
  .korean-text {
    word-break: keep-all;
    line-height: 1.6;
  }

  /* 접근성을 위한 포커스 상태 */
  .focus-visible {
    @apply outline-none ring-2 ring-primary-500 ring-offset-2 ring-offset-white;
  }
}

@layer components {
  /* 버튼 변형 */
  .btn-primary {
    @apply px-4 py-2 bg-primary-600 text-white rounded-md hover:bg-primary-700 focus:outline-none focus:ring-2 focus:ring-primary-500 focus:ring-offset-2 transition-colors;
  }

  .btn-secondary {
    @apply px-4 py-2 bg-gray-200 text-gray-900 rounded-md hover:bg-gray-300 focus:outline-none focus:ring-2 focus:ring-gray-500 focus:ring-offset-2 transition-colors;
  }

  /* 카드 컴포넌트 */
  .card {
    @apply bg-white rounded-lg shadow-md border border-gray-200 overflow-hidden;
  }

  .card-header {
    @apply px-6 py-4 border-b border-gray-200;
  }

  .card-body {
    @apply px-6 py-4;
  }

  /* 입력 스타일 */
  .input {
    @apply block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-primary-500 focus:border-primary-500;
  }

  .input-error {
    @apply border-red-300 focus:ring-red-500 focus:border-red-500;
  }

  /* 한국 모바일 최적화 */
  .mobile-input {
    @apply text-base; /* iOS에서 줌 방지 */
  }

  /* 모바일용 터치 친화적 간격 */
  .touch-target {
    @apply min-h-[44px] min-w-[44px];
  }
}

@layer utilities {
  /* 한국어 텍스트 유틸리티 */
  .break-keep-all {
    word-break: keep-all;
  }

  .text-korean {
    font-family: 'Malgun Gothic', 'Apple SD Gothic Neo', 'Noto Sans KR', sans-serif;
    word-break: keep-all;
    line-height: 1.6;
  }

  /* 애니메이션 유틸리티 */
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

  /* 한국 모바일용 반응형 유틸리티 */
  .mobile-container {
    @apply max-w-sm mx-auto px-4;
  }

  .desktop-container {
    @apply max-w-7xl mx-auto px-4 sm:px-6 lg:px-8;
  }
}
```

## 테스팅 요구사항

### 컴포넌트 테스트 템플릿

```typescript
// tests/components/ui/button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import { describe, it, expect, vi } from 'vitest'
import { Button } from '@/components/ui/button'

describe('Button Component', () => {
  it('올바른 텍스트로 렌더링', () => {
    render(<Button>클릭하세요</Button>)
    expect(screen.getByRole('button', { name: '클릭하세요' })).toBeInTheDocument()
  })

  it('클릭시 onClick 핸들러 호출', () => {
    const handleClick = vi.fn()
    render(<Button onClick={handleClick}>클릭</Button>)

    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })

  it('올바른 변형 클래스 적용', () => {
    render(<Button variant="secondary">버튼</Button>)
    const button = screen.getByRole('button')

    expect(button).toHaveClass('bg-secondary')
    expect(button).not.toHaveClass('bg-primary')
  })

  it('disabled 속성이 true일 때 비활성화', () => {
    render(<Button disabled>비활성화</Button>)
    const button = screen.getByRole('button')

    expect(button).toBeDisabled()
    expect(button).toHaveClass('disabled:opacity-50')
  })

  it('키보드 네비게이션 처리', () => {
    const handleClick = vi.fn()
    render(<Button onClick={handleClick}>키보드 테스트</Button>)

    const button = screen.getByRole('button')
    button.focus()
    fireEvent.keyDown(button, { key: 'Enter', code: 'Enter' })

    expect(handleClick).toHaveBeenCalled()
  })
})
```

### 테스팅 모범 사례

1. **단위 테스트**: 개별 컴포넌트를 격리하여 테스트
2. **통합 테스트**: 컴포넌트 상호작용 및 데이터 흐름 테스트
3. **E2E 테스트**: 중요한 사용자 흐름 테스트 (로그인, 예약, 결제)
4. **커버리지 목표**: 80% 코드 커버리지 목표
5. **테스트 구조**: Arrange-Act-Assert 패턴
6. **외부 의존성 모킹**: API 호출, 라우팅, 외부 서비스

**모킹 전략**:
- MSW(Mock Service Worker)를 사용한 API 호출 모킹
- 위치 테스트를 위한 카카오맵 SDK 모킹
- 결제 흐름 테스트를 위한 토스페이먼츠 모킹
- 실시간 기능을 위한 WebSocket 연결 모킹

## 환경 설정

```bash
# Frontend 환경 변수 (.env.local)

# API 설정
NEXT_PUBLIC_API_URL=http://localhost:3001/api/v1
NEXT_PUBLIC_WS_URL=ws://localhost:3001

# 한국 서비스 API
NEXT_PUBLIC_KAKAO_MAP_API_KEY=your_kakao_map_api_key
NEXT_PUBLIC_TOSS_PAYMENTS_CLIENT_KEY=your_toss_payments_client_key

# 기능 플래그
NEXT_PUBLIC_ENABLE_REAL_TIME=true
NEXT_PUBLIC_ENABLE_DARK_MODE=true
NEXT_PUBLIC_ENABLE_PWA=true

# 분석 및 모니터링
NEXT_PUBLIC_GA_TRACKING_ID=your_google_analytics_id
NEXT_PUBLIC_SENTRY_DSN=your_sentry_dsn

# 개발
NEXT_PUBLIC_ENV=development
NEXT_PUBLIC_DEBUG=true

# 이미지 업로드
NEXT_PUBLIC_MAX_FILE_SIZE=5242880  # 5MB
NEXT_PUBLIC_ALLOWED_FILE_TYPES=image/jpeg,image/png,image/webp

# 지리적 위치
NEXT_PUBLIC_DEFAULT_LAT=37.5665  # 서울 좌표
NEXT_PUBLIC_DEFAULT_LNG=126.9780
NEXT_PUBLIC_SEARCH_RADIUS=10     # 킬로미터
```

## Frontend 개발자 표준

### 중요한 코딩 규칙

1. **TypeScript Strict 모드**: 모든 컴포넌트는 strict 모드가 활성화된 TypeScript 사용 필수
2. **Props 검증**: 모든 컴포넌트 props에 대한 적절한 TypeScript 인터페이스 정의
3. **상태 관리**: 복잡한 상태에는 Zustand 스토어 사용, 로컬 컴포넌트 상태에만 useState 사용
4. **API 호출**: 컴포넌트에서 직접 fetch/axios 호출 금지 - 서비스 레이어 및 훅 사용
5. **에러 경계**: 비동기 작업을 try-catch 블록으로 감싸고 오류를 우아하게 처리
6. **접근성**: 모든 대화형 요소에 적절한 ARIA 레이블 및 키보드 네비게이션 필수
7. **성능**: 불필요한 재렌더링을 방지하기 위해 React.memo, useMemo, useCallback 적절히 사용
8. **반응형 디자인**: 모든 컴포넌트는 모바일 기기에서 작동해야 함 (최소 너비 320px)
9. **한국어 텍스트**: 적절한 한국어 타이포그래피 설정 및 word-break 규칙 사용
10. **로딩 상태**: 비동기 작업에 항상 로딩 인디케이터 표시
11. **에러 상태**: 재시도 옵션과 함께 명확한 한국어 오류 메시지 제공
12. **실시간 업데이트**: 폴링이 아닌 라이브 데이터용 WebSocket 훅 사용

### 빠른 참조

**공통 명령어**:
```bash
# 개발 서버
npm run dev

# 프로덕션 빌드
npm run build

# 테스트 실행
npm test
npm run test:watch
npm run test:coverage

# 타입 체킹
npm run type-check

# 린팅 및 포맷팅
npm run lint
npm run lint:fix
npm run format
```

**주요 Import 패턴**:
```typescript
// UI 컴포넌트
import { Button } from '@/components/ui/button'
import { Card } from '@/components/ui/card'

// 기능 컴포넌트
import { VehicleCard } from '@/components/features/vehicles/vehicle-card'

// 훅
import { useAuth } from '@/hooks/useAuth'
import { useBookings } from '@/hooks/useBookings'

// 스토어
import { useAuthStore } from '@/lib/stores/auth-store'

// 유틸리티
import { cn } from '@/lib/utils'
import { formatCurrency } from '@/lib/utils/format'

// 타입
import type { Vehicle, Booking, User } from '@/types'
```

**파일 명명 규칙**:
- 컴포넌트: `PascalCase.tsx` (예: `VehicleCard.tsx`)
- 훅: `camelCase.ts` (예: `useVehicleSearch.ts`)
- 유틸리티: `camelCase.ts` (예: `formatCurrency.ts`)
- 페이지: `page.tsx` (Next.js App Router)
- 레이아웃: `layout.tsx` (Next.js App Router)

**프로젝트별 패턴**:
- 대화형 컴포넌트에 항상 `'use client'` 지시문 사용
- 더 나은 UX를 위한 적절한 로딩 스켈레톤 구현
- 부드러운 전환 및 애니메이션을 위한 Framer Motion 사용
- Next.js Image 컴포넌트로 적절한 이미지 최적화 구현
- 텍스트 및 포맷팅을 위한 적절한 한국어 지역화 패턴 사용
- 견고한 에러 처리를 위한 적절한 에러 경계 구현