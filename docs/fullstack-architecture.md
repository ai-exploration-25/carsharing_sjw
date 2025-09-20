# 카셰어링 서비스 Fullstack Architecture Document

## Introduction

This document outlines the complete fullstack architecture for 카셰어링 서비스, including backend systems, frontend implementation, and their integration. It serves as the single source of truth for AI-driven development, ensuring consistency across the entire technology stack.

This unified approach combines what would traditionally be separate backend and frontend architecture documents, streamlining the development process for modern fullstack applications where these concerns are increasingly intertwined.

### Starter Template or Existing Project

**Decision**: Greenfield monorepo project with modern fullstack patterns
- **Frontend**: Next.js 14 with TypeScript - custom implementation for Korean market optimization
- **Backend**: Node.js with Express.js and TypeScript - modular monolith architecture
- **Deployment**: AWS infrastructure with Container-based deployment
- **Rationale**: Maximum control over Korean service integrations, performance optimization for local market, and AI-friendly development patterns

### Change Log

| Date | Version | Description | Author |
|------|---------|-------------|---------|
| 2025-09-20 | v1.0 | Initial fullstack architecture creation | Fullstack Architect |

## High Level Architecture

### Technical Summary

The system employs a modern fullstack monorepo architecture deployed on AWS, combining Next.js 14 frontend with Node.js backend in a single repository for optimal development velocity. The frontend leverages server-side rendering for SEO and performance while the backend uses a modular monolith pattern with clear service boundaries. Real-time features are implemented through WebSocket connections for booking notifications and status updates. The architecture seamlessly integrates Korean market services (Kakao Map, Toss Payments) and supports hybrid B2B/B2C user workflows with role-based access control. The entire stack is TypeScript-first for type safety across the application boundary, with shared types and utilities to ensure consistency between frontend and backend implementations.

### Platform and Infrastructure Choice

**Platform**: AWS Full Stack
**Key Services**: ECS Fargate, RDS PostgreSQL, ElastiCache Redis, S3, CloudFront, Application Load Balancer, Route 53
**Deployment Host and Regions**: ap-northeast-2 (Seoul) primary, ap-northeast-1 (Tokyo) backup

**Rationale**: AWS provides the most comprehensive Korean region support with low-latency connectivity, excellent integration with Korean payment systems, and robust enterprise-grade services required for financial transactions. ECS Fargate eliminates server management overhead while providing excellent scalability.

### Repository Structure

**Structure**: Monorepo with clear separation of concerns
**Monorepo Tool**: npm workspaces with custom build scripts
**Package Organization**: Apps (frontend/backend) + shared packages (types, utilities, UI components)

**Benefits**:
- Shared TypeScript types between frontend and backend
- Atomic deployments of related changes
- Simplified dependency management
- Enhanced developer experience with unified tooling

### High Level Architecture Diagram

```mermaid
graph TB
    subgraph "Client Layer"
        WEB[Web Browser]
        MOB[Mobile Browser]
        PWA[PWA App]
    end

    subgraph "CDN & Edge"
        CF[CloudFront CDN]
        EDGE[Edge Locations]
    end

    subgraph "Load Balancing"
        ALB[Application Load Balancer]
        TG1[Frontend Target Group]
        TG2[Backend Target Group]
    end

    subgraph "Application Layer - ECS Fargate"
        subgraph "Frontend Cluster"
            FE1[Next.js Instance 1]
            FE2[Next.js Instance 2]
        end
        subgraph "Backend Cluster"
            BE1[Node.js API Instance 1]
            BE2[Node.js API Instance 2]
        end
        subgraph "WebSocket Cluster"
            WS1[Socket.io Instance 1]
            WS2[Socket.io Instance 2]
        end
    end

    subgraph "Data Layer"
        RDS[(RDS PostgreSQL)]
        REDIS[(ElastiCache Redis)]
        S3[S3 Storage]
    end

    subgraph "External Services"
        KAKAO[Kakao Map API]
        TOSS[Toss Payments API]
        FCM[Firebase FCM]
    end

    subgraph "Monitoring & Logging"
        CW[CloudWatch]
        SENTRY[Sentry]
    end

    WEB --> CF
    MOB --> CF
    PWA --> CF
    CF --> EDGE
    EDGE --> ALB

    ALB --> TG1
    ALB --> TG2
    TG1 --> FE1
    TG1 --> FE2
    TG2 --> BE1
    TG2 --> BE2

    FE1 -.->|WebSocket| WS1
    FE2 -.->|WebSocket| WS2
    BE1 --> RDS
    BE2 --> RDS
    BE1 --> REDIS
    BE2 --> REDIS
    WS1 --> REDIS
    WS2 --> REDIS

    BE1 --> S3
    BE2 --> S3
    BE1 --> KAKAO
    BE1 --> TOSS
    BE1 --> FCM

    FE1 --> CW
    BE1 --> CW
    FE1 --> SENTRY
    BE1 --> SENTRY
```

### Architectural Patterns

- **Monorepo Architecture**: Single repository with multiple applications and shared packages - *Rationale*: Enables code sharing, atomic deployments, and simplified dependency management for fullstack development
- **Modular Monolith Backend**: Clear module boundaries with potential for microservices extraction - *Rationale*: Faster development than microservices while maintaining architectural flexibility for future scaling
- **Component-Based UI**: Reusable React components with TypeScript - *Rationale*: Maintainability and type safety across large codebases with shared design system
- **API Gateway Pattern**: Single entry point for all API calls through Load Balancer - *Rationale*: Centralized auth, rate limiting, and monitoring with simplified client configuration
- **Repository Pattern**: Abstract data access logic - *Rationale*: Enables testing and future database migration flexibility
- **Event-Driven Communication**: Internal event bus for module communication - *Rationale*: Reduces coupling between modules and supports async processing
- **BFF (Backend for Frontend)**: Tailored API responses for web and mobile clients - *Rationale*: Optimizes data transfer and reduces over-fetching for different client needs

## Tech Stack

### Technology Stack Table

| Category | Technology | Version | Purpose | Rationale |
|----------|------------|---------|---------|-----------|
| Frontend Language | TypeScript | 5.3.3 | Type-safe frontend development | Excellent tooling, catches errors at compile time, shared types with backend |
| Frontend Framework | Next.js | 14.0.4 | React meta-framework with SSR/SSG | SEO benefits, excellent performance, Korean market proven |
| UI Component Library | Headless UI | 1.7.17 | Accessible unstyled components | Full accessibility support, Tailwind compatibility, customizable |
| State Management | Zustand | 4.4.7 | Lightweight state management | Simple API, TypeScript support, better than Redux for MVP |
| Backend Language | TypeScript | 5.3.3 | Type-safe backend development | Consistency with frontend, excellent IDE support, reduced runtime errors |
| Backend Framework | Express.js | 4.18.2 | Web application framework | Mature, flexible, excellent middleware ecosystem |
| API Style | REST | OpenAPI 3.0 | HTTP API architecture | Simple, well-understood, excellent tooling support |
| Database | PostgreSQL | 15.5 | Primary relational database | ACID compliance, JSON support, excellent performance, Korean timezone support |
| Cache | Redis | 7.2.3 | In-memory caching and sessions | Fast performance, pub/sub for real-time features, session management |
| File Storage | AWS S3 | Latest | Object storage for images | Scalable, cost-effective, CDN integration, Korean region availability |
| Authentication | JWT | jsonwebtoken@9.0.2 | Stateless authentication | Scalable, secure, works well with SPAs and mobile apps |
| Frontend Testing | Jest + Testing Library | 29.7.0 + 14.1.2 | Component and unit testing | React-focused testing, excellent mocking, community standard |
| Backend Testing | Jest + Supertest | 29.7.0 + 6.3.3 | API and integration testing | Comprehensive testing framework, great TypeScript support |
| E2E Testing | Playwright | 1.40.1 | End-to-end browser testing | Cross-browser support, excellent Korean language testing |
| Build Tool | Next.js + tsc | 14.0.4 + 5.3.3 | Compilation and bundling | Zero-config React builds, TypeScript compilation, optimization |
| Bundler | Webpack (via Next.js) | 5.89.0 | Module bundling | Built into Next.js, tree shaking, code splitting |
| IaC Tool | AWS CDK | 2.100.0 | Infrastructure as Code | Type-safe infrastructure, AWS best practices, version control |
| CI/CD | GitHub Actions | Latest | Continuous integration/deployment | Free for public repos, excellent GitHub integration, Korean support |
| Monitoring | CloudWatch + Sentry | Latest | Application monitoring | AWS native monitoring + error tracking with Korean localization |
| Logging | Winston | 3.11.0 | Structured logging | Multiple transports, structured JSON logs, production-ready |
| CSS Framework | Tailwind CSS | 3.3.6 | Utility-first styling | Rapid development, consistent design, mobile-first approach |

## Data Models

### User

**Purpose**: Core user entity supporting both hosts (corporate users) and guests (individual users) with Korean market specifics

**Key Attributes**:
- id: UUID - Unique identifier
- email: string - Login credential and communication
- password_hash: string - Secure password storage with bcrypt
- name: string - Korean name support (한글 이름)
- phone: string - Korean mobile number format (010-XXXX-XXXX)
- role: enum - USER_ROLE (guest, host, admin)
- profile_image_url: string? - S3 stored profile picture
- is_verified: boolean - Email and phone verification status
- created_at: timestamp - Account creation with Korean timezone
- updated_at: timestamp - Last modification timestamp

#### TypeScript Interface

```typescript
interface User {
  id: string
  email: string
  name: string
  phone: string
  role: 'guest' | 'host' | 'admin'
  profile_image_url?: string
  is_verified: boolean
  created_at: string
  updated_at: string
  corporate_profile?: CorporateProfile
}

interface UserCreateData {
  email: string
  password: string
  name: string
  phone: string
  role: 'guest' | 'host'
}
```

#### Relationships
- Has one: CorporateProfile (if host)
- Has many: Vehicles (if host), Bookings, Reviews

### Vehicle

**Purpose**: Represents cars available for sharing with Korean vehicle specifications and location data

**Key Attributes**:
- id: UUID - Unique identifier
- owner_id: UUID - Reference to User (host)
- license_plate: string - Korean license plate format
- make: string - 제조사 (Genesis, Hyundai, Kia, etc.)
- model: string - 모델명 (G90, Sonata, K5, etc.)
- year: integer - 연식
- fuel_type: enum - FUEL_TYPE (gasoline, diesel, electric, hybrid)
- hourly_rate: decimal - 시간당 대여료 (KRW)
- location_address: string - Korean address format
- location_lat: decimal - GPS 위도 (Korean coordinate system)
- location_lng: decimal - GPS 경도 (Korean coordinate system)
- images: string[] - S3 image URLs array
- status: enum - VEHICLE_STATUS (active, inactive, maintenance)

#### TypeScript Interface

```typescript
interface Vehicle {
  id: string
  owner_id: string
  license_plate: string
  make: string
  model: string
  year: number
  color: string
  fuel_type: 'gasoline' | 'diesel' | 'electric' | 'hybrid'
  transmission: 'manual' | 'automatic'
  hourly_rate: number
  location_address: string
  location_lat: number
  location_lng: number
  status: 'active' | 'inactive' | 'maintenance'
  images: string[]
  description?: string
  owner: User
  created_at: string
  updated_at: string
}

interface VehicleSearchParams {
  lat: number
  lng: number
  start_datetime: string
  end_datetime: string
  radius?: number
  fuel_type?: string[]
  price_min?: number
  price_max?: number
}
```

#### Relationships
- Belongs to: User (owner)
- Has many: Bookings, VehicleAvailabilities, Reviews

### Booking

**Purpose**: Represents car rental reservations with Korean business logic and payment integration

**Key Attributes**:
- id: UUID - Unique identifier
- vehicle_id: UUID - Reference to Vehicle
- guest_id: UUID - Reference to User (guest)
- host_id: UUID - Reference to User (host)
- start_datetime: timestamp - 대여 시작 시간 (Korean timezone)
- end_datetime: timestamp - 대여 종료 시간 (Korean timezone)
- total_amount: decimal - 총 결제 금액 (KRW)
- platform_fee: decimal - 플랫폼 수수료 (15%)
- status: enum - BOOKING_STATUS (pending, approved, rejected, active, completed, cancelled)
- guest_message: string? - 게스트 메시지
- host_response: string? - 호스트 응답

#### TypeScript Interface

```typescript
interface Booking {
  id: string
  vehicle_id: string
  guest_id: string
  host_id: string
  start_datetime: string
  end_datetime: string
  total_amount: number
  platform_fee: number
  status: 'pending' | 'approved' | 'rejected' | 'active' | 'completed' | 'cancelled'
  guest_message?: string
  host_response?: string
  pickup_photos?: string[]
  return_photos?: string[]
  vehicle: Vehicle
  guest: User
  host: User
  payment?: Payment
  created_at: string
  updated_at: string
}

interface BookingCreateData {
  vehicle_id: string
  start_datetime: string
  end_datetime: string
  guest_message?: string
}
```

#### Relationships
- Belongs to: Vehicle, User (guest), User (host)
- Has one: Payment
- Has many: Reviews

## API Specification

### REST API Specification

```yaml
openapi: 3.0.0
info:
  title: 카셰어링 서비스 Fullstack API
  version: 1.0.0
  description: Complete API specification for Korean car sharing platform
  contact:
    name: Development Team
    email: dev@carshare.co.kr
servers:
  - url: https://api.carshare.co.kr/v1
    description: Production server
  - url: https://api-staging.carshare.co.kr/v1
    description: Staging server
  - url: http://localhost:3001/api/v1
    description: Development server

paths:
  # Authentication endpoints
  /auth/register:
    post:
      summary: User registration
      tags: [Authentication]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UserRegistration'
      responses:
        '201':
          description: User created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthResponse'
        '400':
          description: Validation error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

  /auth/login:
    post:
      summary: User authentication
      tags: [Authentication]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/LoginRequest'
      responses:
        '200':
          description: Login successful
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthResponse'

  /auth/refresh:
    post:
      summary: Refresh access token
      tags: [Authentication]
      security:
        - bearerAuth: []
      responses:
        '200':
          description: Token refreshed successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthResponse'

  # Vehicle endpoints
  /vehicles/search:
    get:
      summary: Search available vehicles
      tags: [Vehicles]
      parameters:
        - name: lat
          in: query
          required: true
          schema:
            type: number
            example: 37.5665
        - name: lng
          in: query
          required: true
          schema:
            type: number
            example: 126.9780
        - name: start_datetime
          in: query
          required: true
          schema:
            type: string
            format: date-time
        - name: end_datetime
          in: query
          required: true
          schema:
            type: string
            format: date-time
        - name: radius
          in: query
          schema:
            type: number
            default: 5
            description: Search radius in kilometers
        - name: fuel_type
          in: query
          schema:
            type: array
            items:
              type: string
              enum: [gasoline, diesel, electric, hybrid]
        - name: price_min
          in: query
          schema:
            type: number
        - name: price_max
          in: query
          schema:
            type: number
      responses:
        '200':
          description: Available vehicles
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/VehicleSearchResponse'

  /vehicles:
    post:
      summary: Register new vehicle (Host only)
      tags: [Vehicles]
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/VehicleCreateRequest'
      responses:
        '201':
          description: Vehicle created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/VehicleResponse'

  /vehicles/{id}:
    get:
      summary: Get vehicle details
      tags: [Vehicles]
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '200':
          description: Vehicle details
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/VehicleResponse'

    put:
      summary: Update vehicle (Host only)
      tags: [Vehicles]
      security:
        - bearerAuth: []
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
            format: uuid
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/VehicleUpdateRequest'
      responses:
        '200':
          description: Vehicle updated successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/VehicleResponse'

  # Booking endpoints
  /bookings:
    get:
      summary: Get user bookings
      tags: [Bookings]
      security:
        - bearerAuth: []
      parameters:
        - name: status
          in: query
          schema:
            type: string
            enum: [pending, approved, active, completed, cancelled]
        - name: role
          in: query
          schema:
            type: string
            enum: [guest, host]
            description: Get bookings as guest or host
      responses:
        '200':
          description: User bookings
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/BookingListResponse'

    post:
      summary: Create booking request
      tags: [Bookings]
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/BookingCreateRequest'
      responses:
        '201':
          description: Booking created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/BookingResponse'

  /bookings/{id}/approve:
    put:
      summary: Approve booking (Host only)
      tags: [Bookings]
      security:
        - bearerAuth: []
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
            format: uuid
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                host_response:
                  type: string
                  description: Optional response message
      responses:
        '200':
          description: Booking approved successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/BookingResponse'

  # Payment endpoints
  /payments/process:
    post:
      summary: Process payment for booking
      tags: [Payments]
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PaymentRequest'
      responses:
        '200':
          description: Payment processed successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PaymentResponse'

  /payments/webhook/toss:
    post:
      summary: Toss Payments webhook
      tags: [Payments]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                paymentKey:
                  type: string
                orderId:
                  type: string
                amount:
                  type: number
      responses:
        '200':
          description: Webhook processed successfully

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:
    # Authentication schemas
    UserRegistration:
      type: object
      required: [email, password, name, phone, role]
      properties:
        email:
          type: string
          format: email
          example: "user@example.com"
        password:
          type: string
          minLength: 8
          example: "password123"
        name:
          type: string
          example: "김철수"
        phone:
          type: string
          pattern: '^010-\d{4}-\d{4}$'
          example: "010-1234-5678"
        role:
          type: string
          enum: [guest, host]

    LoginRequest:
      type: object
      required: [email, password]
      properties:
        email:
          type: string
          format: email
        password:
          type: string

    AuthResponse:
      type: object
      properties:
        access_token:
          type: string
        refresh_token:
          type: string
        user:
          $ref: '#/components/schemas/User'

    # User schemas
    User:
      type: object
      properties:
        id:
          type: string
          format: uuid
        email:
          type: string
          format: email
        name:
          type: string
        phone:
          type: string
        role:
          type: string
          enum: [guest, host, admin]
        profile_image_url:
          type: string
        is_verified:
          type: boolean
        created_at:
          type: string
          format: date-time
        updated_at:
          type: string
          format: date-time

    # Vehicle schemas
    VehicleCreateRequest:
      type: object
      required: [license_plate, make, model, year, hourly_rate, location_address]
      properties:
        license_plate:
          type: string
          example: "12가3456"
        make:
          type: string
          example: "Genesis"
        model:
          type: string
          example: "G90"
        year:
          type: integer
          example: 2022
        color:
          type: string
          example: "검정색"
        fuel_type:
          type: string
          enum: [gasoline, diesel, electric, hybrid]
        transmission:
          type: string
          enum: [manual, automatic]
        hourly_rate:
          type: number
          example: 15000
        location_address:
          type: string
          example: "서울특별시 강남구 테헤란로 427"
        description:
          type: string

    VehicleUpdateRequest:
      type: object
      properties:
        hourly_rate:
          type: number
        location_address:
          type: string
        description:
          type: string
        status:
          type: string
          enum: [active, inactive, maintenance]

    VehicleResponse:
      type: object
      properties:
        id:
          type: string
          format: uuid
        license_plate:
          type: string
        make:
          type: string
        model:
          type: string
        year:
          type: integer
        color:
          type: string
        fuel_type:
          type: string
        transmission:
          type: string
        hourly_rate:
          type: number
        location_address:
          type: string
        location_lat:
          type: number
        location_lng:
          type: number
        status:
          type: string
        images:
          type: array
          items:
            type: string
        description:
          type: string
        owner:
          $ref: '#/components/schemas/User'
        created_at:
          type: string
          format: date-time

    VehicleSearchResponse:
      type: object
      properties:
        vehicles:
          type: array
          items:
            $ref: '#/components/schemas/VehicleResponse'
        total:
          type: integer
        page:
          type: integer
        limit:
          type: integer

    # Booking schemas
    BookingCreateRequest:
      type: object
      required: [vehicle_id, start_datetime, end_datetime]
      properties:
        vehicle_id:
          type: string
          format: uuid
        start_datetime:
          type: string
          format: date-time
        end_datetime:
          type: string
          format: date-time
        guest_message:
          type: string

    BookingResponse:
      type: object
      properties:
        id:
          type: string
          format: uuid
        vehicle:
          $ref: '#/components/schemas/VehicleResponse'
        guest:
          $ref: '#/components/schemas/User'
        host:
          $ref: '#/components/schemas/User'
        start_datetime:
          type: string
          format: date-time
        end_datetime:
          type: string
          format: date-time
        total_amount:
          type: number
        platform_fee:
          type: number
        status:
          type: string
          enum: [pending, approved, rejected, active, completed, cancelled]
        guest_message:
          type: string
        host_response:
          type: string
        created_at:
          type: string
          format: date-time

    BookingListResponse:
      type: object
      properties:
        bookings:
          type: array
          items:
            $ref: '#/components/schemas/BookingResponse'
        total:
          type: integer

    # Payment schemas
    PaymentRequest:
      type: object
      required: [booking_id, payment_method]
      properties:
        booking_id:
          type: string
          format: uuid
        payment_method:
          type: string
          enum: [card, bank_transfer, kakao_pay, toss_pay]

    PaymentResponse:
      type: object
      properties:
        id:
          type: string
          format: uuid
        booking_id:
          type: string
          format: uuid
        amount:
          type: number
        platform_fee:
          type: number
        host_amount:
          type: number
        payment_method:
          type: string
        status:
          type: string
          enum: [pending, completed, failed, refunded]
        toss_payment_url:
          type: string
          description: Redirect URL for Toss Payments

    # Error schemas
    ErrorResponse:
      type: object
      properties:
        error:
          type: object
          properties:
            code:
              type: string
            message:
              type: string
            details:
              type: object
            timestamp:
              type: string
              format: date-time
            request_id:
              type: string
```

## Components

### Frontend Web Application

**Responsibility**: User interface for car sharing platform with responsive design and real-time updates

**Key Interfaces**:
- Next.js pages and API routes
- React components with TypeScript
- WebSocket connections for real-time updates
- External service integrations (Kakao Map, Toss Payments)

**Dependencies**: Backend API, Kakao Map SDK, Toss Payments SDK, Firebase FCM

**Technology Stack**: Next.js 14, React 18, TypeScript, Tailwind CSS, Zustand, React Hook Form

### Backend API Service

**Responsibility**: Business logic, data management, and external service integration

**Key Interfaces**:
- REST API endpoints
- WebSocket connections
- Database operations
- External API integrations

**Dependencies**: PostgreSQL, Redis, AWS S3, Kakao Map API, Toss Payments API, Firebase FCM

**Technology Stack**: Node.js, Express.js, TypeScript, Prisma ORM, Socket.io, Winston

### Shared Packages

**Responsibility**: Common types, utilities, and constants shared between frontend and backend

**Key Interfaces**:
- TypeScript type definitions
- Validation schemas
- Utility functions
- Constants and enums

**Dependencies**: None (pure TypeScript/JavaScript)

**Technology Stack**: TypeScript, Joi validation schemas, date-fns utilities

### Component Diagrams

```mermaid
graph TB
    subgraph "Monorepo Structure"
        subgraph "Apps"
            FE[Frontend App<br/>Next.js + React]
            BE[Backend App<br/>Express.js API]
        end

        subgraph "Shared Packages"
            TYPES[Shared Types<br/>TypeScript interfaces]
            UTILS[Shared Utils<br/>Common functions]
            UI[UI Components<br/>Reusable components]
            CONFIG[Config<br/>Environment & constants]
        end
    end

    subgraph "External Services"
        KAKAO[Kakao Map API]
        TOSS[Toss Payments]
        FCM[Firebase FCM]
        AWS[AWS Services]
    end

    FE --> TYPES
    FE --> UTILS
    FE --> UI
    FE --> CONFIG
    BE --> TYPES
    BE --> UTILS
    BE --> CONFIG

    FE -.->|HTTP/WebSocket| BE
    BE --> KAKAO
    BE --> TOSS
    BE --> FCM
    BE --> AWS
    FE --> KAKAO
    FE --> TOSS
```

## External APIs

### Kakao Map API

- **Purpose**: Location services, geocoding, and interactive maps for Korean users
- **Documentation**: https://developers.kakao.com/docs/latest/ko/local/dev-guide
- **Base URL(s)**: https://dapi.kakao.com
- **Authentication**: REST API Key in Authorization header
- **Rate Limits**: 100,000 requests/day (free tier)

**Key Endpoints Used**:
- `GET /v2/local/search/address.json` - Address to coordinates conversion
- `GET /v2/local/geo/coord2address.json` - Coordinates to address conversion
- `GET /v2/local/search/keyword.json` - Location search by keyword

**Integration Notes**: Frontend uses JavaScript SDK for interactive maps, backend uses REST API for geocoding. Rate limiting implemented with Redis caching.

### Toss Payments API

- **Purpose**: Payment processing optimized for Korean market with multiple payment methods
- **Documentation**: https://docs.tosspayments.com/reference
- **Base URL(s)**: https://api.tosspayments.com
- **Authentication**: Bearer token with secret key
- **Rate Limits**: No specific limits documented

**Key Endpoints Used**:
- `POST /v1/payments` - Process payment (card, bank transfer, Kakao Pay)
- `GET /v1/payments/{paymentKey}` - Payment status inquiry
- `POST /v1/payments/{paymentKey}/cancel` - Cancel/refund payment

**Integration Notes**: Frontend redirects to Toss payment page, backend handles webhooks for payment status updates. 15% platform fee automatically calculated.

### Firebase Cloud Messaging API

- **Purpose**: Real-time push notifications for booking updates and system alerts
- **Documentation**: https://firebase.google.com/docs/cloud-messaging
- **Base URL(s)**: https://fcm.googleapis.com
- **Authentication**: Service account key
- **Rate Limits**: 1,000,000 messages/month (free tier)

**Key Endpoints Used**:
- `POST /v1/projects/{project-id}/messages:send` - Send notification

**Integration Notes**: Supports both web push notifications and future mobile app notifications. Integrated with WebSocket for real-time updates.

## Core Workflows

### Complete Booking Flow

```mermaid
sequenceDiagram
    participant U as User (Guest)
    participant FE as Frontend
    participant BE as Backend
    participant DB as PostgreSQL
    participant KAKAO as Kakao Map
    participant H as Host
    participant WS as WebSocket
    participant TOSS as Toss Payments

    U->>FE: Search vehicles
    FE->>BE: GET /vehicles/search
    BE->>KAKAO: Geocode search area
    BE->>DB: Query available vehicles
    DB->>BE: Vehicle results
    BE->>FE: Vehicle list with location
    FE->>U: Display vehicles on map

    U->>FE: Select vehicle & dates
    FE->>BE: POST /bookings
    BE->>DB: Create booking (pending)
    BE->>WS: Notify host via WebSocket
    WS->>H: Real-time booking notification
    BE->>FE: Booking created (pending)

    H->>WS: Approve booking
    WS->>BE: Booking approval
    BE->>DB: Update booking status
    BE->>WS: Notify guest
    WS->>U: Booking approved notification

    U->>FE: Proceed to payment
    FE->>BE: POST /payments/process
    BE->>TOSS: Create payment request
    TOSS->>BE: Payment URL
    BE->>FE: Redirect to Toss
    FE->>U: Redirect to payment

    U->>TOSS: Complete payment
    TOSS->>BE: Payment webhook
    BE->>DB: Update payment status
    BE->>WS: Payment success notification
    WS->>U: Booking confirmed
    WS->>H: Payment received notification
```

### Real-time Notification Flow

```mermaid
sequenceDiagram
    participant FE as Frontend
    participant WS as WebSocket Server
    participant REDIS as Redis Pub/Sub
    participant BE as Backend API
    participant FCM as Firebase FCM

    FE->>WS: Connect WebSocket
    WS->>REDIS: Subscribe to user channel

    BE->>REDIS: Publish notification event
    REDIS->>WS: Event received
    WS->>FE: Real-time notification
    FE->>FE: Update UI state

    WS->>FCM: Send push notification
    FCM->>FE: Browser push notification
```

## Database Schema

```sql
-- Enable UUID extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- User authentication and profiles
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    name VARCHAR(100) NOT NULL,
    phone VARCHAR(20) NOT NULL,
    role VARCHAR(20) NOT NULL CHECK (role IN ('guest', 'host', 'admin')),
    profile_image_url TEXT,
    is_verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Corporate profile for hosts
CREATE TABLE corporate_profiles (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    company_name VARCHAR(200) NOT NULL,
    business_registration_number VARCHAR(20) UNIQUE NOT NULL,
    tax_id VARCHAR(50),
    is_verified BOOLEAN DEFAULT FALSE,
    verification_documents JSONB,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Vehicle information
CREATE TABLE vehicles (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    owner_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    license_plate VARCHAR(20) UNIQUE NOT NULL,
    make VARCHAR(50) NOT NULL,
    model VARCHAR(50) NOT NULL,
    year INTEGER NOT NULL CHECK (year >= 1990 AND year <= EXTRACT(YEAR FROM NOW()) + 1),
    color VARCHAR(30),
    fuel_type VARCHAR(20) CHECK (fuel_type IN ('gasoline', 'diesel', 'electric', 'hybrid')),
    transmission VARCHAR(20) CHECK (transmission IN ('manual', 'automatic')),
    hourly_rate DECIMAL(10,2) NOT NULL CHECK (hourly_rate > 0),
    location_address TEXT NOT NULL,
    location_lat DECIMAL(10,8) NOT NULL,
    location_lng DECIMAL(11,8) NOT NULL,
    status VARCHAR(20) DEFAULT 'active' CHECK (status IN ('active', 'inactive', 'maintenance')),
    images JSONB DEFAULT '[]'::jsonb,
    description TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Vehicle availability schedules
CREATE TABLE vehicle_availabilities (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    vehicle_id UUID NOT NULL REFERENCES vehicles(id) ON DELETE CASCADE,
    day_of_week INTEGER CHECK (day_of_week >= 0 AND day_of_week <= 6),
    start_time TIME,
    end_time TIME,
    is_recurring BOOLEAN DEFAULT TRUE,
    specific_date DATE,
    is_blocked BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT check_recurring_or_specific CHECK (
        (is_recurring = TRUE AND day_of_week IS NOT NULL AND specific_date IS NULL) OR
        (is_recurring = FALSE AND specific_date IS NOT NULL AND day_of_week IS NULL)
    ),
    CONSTRAINT check_time_order CHECK (start_time < end_time)
);

-- Booking reservations
CREATE TABLE bookings (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    vehicle_id UUID NOT NULL REFERENCES vehicles(id),
    guest_id UUID NOT NULL REFERENCES users(id),
    host_id UUID NOT NULL REFERENCES users(id),
    start_datetime TIMESTAMP WITH TIME ZONE NOT NULL,
    end_datetime TIMESTAMP WITH TIME ZONE NOT NULL,
    total_amount DECIMAL(10,2) NOT NULL CHECK (total_amount > 0),
    platform_fee DECIMAL(10,2) NOT NULL CHECK (platform_fee >= 0),
    status VARCHAR(20) DEFAULT 'pending' CHECK (status IN ('pending', 'approved', 'rejected', 'active', 'completed', 'cancelled')),
    guest_message TEXT,
    host_response TEXT,
    pickup_photos JSONB DEFAULT '[]'::jsonb,
    return_photos JSONB DEFAULT '[]'::jsonb,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    CONSTRAINT check_datetime_order CHECK (start_datetime < end_datetime),
    CONSTRAINT check_future_booking CHECK (start_datetime > NOW())
);

-- Payment transactions
CREATE TABLE payments (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    booking_id UUID NOT NULL REFERENCES bookings(id),
    amount DECIMAL(10,2) NOT NULL CHECK (amount > 0),
    platform_fee DECIMAL(10,2) NOT NULL CHECK (platform_fee >= 0),
    host_amount DECIMAL(10,2) NOT NULL CHECK (host_amount >= 0),
    payment_method VARCHAR(50),
    toss_payment_key VARCHAR(100),
    toss_order_id VARCHAR(100),
    status VARCHAR(20) DEFAULT 'pending' CHECK (status IN ('pending', 'completed', 'failed', 'refunded')),
    paid_at TIMESTAMP WITH TIME ZONE,
    settled_at TIMESTAMP WITH TIME ZONE,
    failure_reason TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Reviews and ratings
CREATE TABLE reviews (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    booking_id UUID NOT NULL REFERENCES bookings(id),
    reviewer_id UUID NOT NULL REFERENCES users(id),
    reviewee_id UUID NOT NULL REFERENCES users(id),
    rating INTEGER NOT NULL CHECK (rating >= 1 AND rating <= 5),
    category_ratings JSONB,
    comment TEXT,
    is_recommended BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    UNIQUE(booking_id, reviewer_id)
);

-- Notifications for real-time features
CREATE TABLE notifications (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    type VARCHAR(50) NOT NULL,
    title VARCHAR(200) NOT NULL,
    message TEXT NOT NULL,
    data JSONB,
    is_read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes for performance optimization
CREATE INDEX idx_vehicles_location ON vehicles(location_lat, location_lng);
CREATE INDEX idx_vehicles_status_active ON vehicles(status) WHERE status = 'active';
CREATE INDEX idx_bookings_datetime ON bookings(start_datetime, end_datetime);
CREATE INDEX idx_bookings_status ON bookings(status);
CREATE INDEX idx_bookings_guest ON bookings(guest_id);
CREATE INDEX idx_bookings_host ON bookings(host_id);
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_phone ON users(phone);
CREATE INDEX idx_payments_status ON payments(status);
CREATE INDEX idx_notifications_user_unread ON notifications(user_id, is_read) WHERE is_read = FALSE;

-- Full-text search for vehicles (Korean support)
CREATE INDEX idx_vehicles_search ON vehicles USING GIN(
    to_tsvector('korean', make || ' ' || model || ' ' || COALESCE(description, ''))
);

-- Triggers for updated_at timestamps
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ language 'plpgsql';

CREATE TRIGGER update_users_updated_at BEFORE UPDATE ON users
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_vehicles_updated_at BEFORE UPDATE ON vehicles
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_bookings_updated_at BEFORE UPDATE ON bookings
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_payments_updated_at BEFORE UPDATE ON payments
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

## Frontend Architecture

### Component Architecture

#### Component Organization

```
components/
├── ui/                             # Base reusable components
│   ├── button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx
│   │   └── index.ts
│   ├── input/
│   ├── modal/
│   └── card/
├── features/                       # Feature-specific components
│   ├── vehicles/
│   │   ├── VehicleCard/
│   │   ├── VehicleSearch/
│   │   ├── VehicleGallery/
│   │   └── VehicleMap/
│   ├── bookings/
│   ├── payments/
│   └── auth/
└── layout/                         # Layout components
    ├── Header/
    ├── Footer/
    ├── Sidebar/
    └── Navigation/
```

#### Component Template

```typescript
'use client'

import { FC, ReactNode } from 'react'
import { cn } from '@/lib/utils'

interface VehicleCardProps {
  vehicle: Vehicle
  className?: string
  onClick?: (vehicle: Vehicle) => void
  showOwner?: boolean
}

export const VehicleCard: FC<VehicleCardProps> = ({
  vehicle,
  className,
  onClick,
  showOwner = false,
}) => {
  const handleClick = () => {
    onClick?.(vehicle)
  }

  return (
    <div
      className={cn(
        'bg-white rounded-lg shadow-md border border-gray-200 overflow-hidden cursor-pointer transition-transform hover:scale-105',
        className
      )}
      onClick={handleClick}
    >
      <div className="aspect-video relative">
        <img
          src={vehicle.images[0] || '/images/cars/placeholder.jpg'}
          alt={`${vehicle.make} ${vehicle.model}`}
          className="w-full h-full object-cover"
        />
      </div>
      <div className="p-4">
        <h3 className="font-semibold text-lg text-gray-900">
          {vehicle.make} {vehicle.model}
        </h3>
        <p className="text-gray-600 text-sm">{vehicle.year}년식</p>
        <div className="mt-2 flex justify-between items-center">
          <span className="text-primary-600 font-bold">
            시간당 {formatCurrency(vehicle.hourly_rate)}
          </span>
          {showOwner && (
            <span className="text-gray-500 text-sm">
              {vehicle.owner.name}
            </span>
          )}
        </div>
      </div>
    </div>
  )
}

export default VehicleCard
```

### State Management Architecture

#### State Structure

```typescript
// lib/stores/auth-store.ts
interface AuthState {
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
}

// lib/stores/vehicle-store.ts
interface VehicleState {
  searchResults: Vehicle[]
  filters: VehicleFilters
  isLoading: boolean
  selectedVehicle: Vehicle | null

  // Actions
  searchVehicles: (params: VehicleSearchParams) => Promise<void>
  setFilters: (filters: Partial<VehicleFilters>) => void
  selectVehicle: (vehicle: Vehicle) => void
}

// lib/stores/booking-store.ts
interface BookingState {
  bookings: Booking[]
  currentBooking: Booking | null
  isLoading: boolean

  // Actions
  createBooking: (data: BookingCreateData) => Promise<void>
  fetchBookings: () => Promise<void>
  approveBooking: (id: string, response?: string) => Promise<void>
}
```

#### State Management Patterns

- **Local State**: Use useState for component-specific UI state
- **Global State**: Use Zustand stores for shared application state
- **Server State**: Use custom hooks with React Query patterns
- **Form State**: Use React Hook Form for complex forms
- **Persistent State**: Use Zustand persist middleware for auth and preferences

### Routing Architecture

#### Route Organization

```
app/
├── (auth)/                         # Auth group
│   ├── login/page.tsx
│   ├── register/page.tsx
│   └── layout.tsx
├── (dashboard)/                    # Protected dashboard
│   ├── guest/
│   │   ├── page.tsx               # Guest dashboard
│   │   ├── bookings/page.tsx
│   │   └── profile/page.tsx
│   ├── host/
│   │   ├── page.tsx               # Host dashboard
│   │   ├── vehicles/
│   │   │   ├── page.tsx
│   │   │   ├── create/page.tsx
│   │   │   └── [id]/edit/page.tsx
│   │   ├── bookings/page.tsx
│   │   └── earnings/page.tsx
│   └── layout.tsx                 # Protected layout
├── vehicles/
│   ├── search/page.tsx
│   └── [id]/page.tsx
├── page.tsx                       # Home page
├── layout.tsx                     # Root layout
└── loading.tsx                    # Global loading
```

#### Protected Route Pattern

```typescript
// app/(dashboard)/layout.tsx
import { AuthGuard } from '@/components/features/auth/auth-guard'

export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <AuthGuard requireAuth>
      <div className="min-h-screen bg-gray-50">
        <DashboardNavigation />
        <main className="py-6">
          {children}
        </main>
      </div>
    </AuthGuard>
  )
}
```

### Frontend Services Layer

#### API Client Setup

```typescript
// lib/api/client.ts
import axios from 'axios'
import { useAuthStore } from '@/lib/stores/auth-store'

export const apiClient = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  timeout: 10000,
})

// Request interceptor
apiClient.interceptors.request.use((config) => {
  const token = useAuthStore.getState().token
  if (token) {
    config.headers.Authorization = `Bearer ${token}`
  }
  return config
})

// Response interceptor
apiClient.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response?.status === 401) {
      useAuthStore.getState().logout()
      window.location.href = '/login'
    }
    return Promise.reject(error)
  }
)
```

#### Service Example

```typescript
// lib/api/vehicles.ts
import { apiClient } from './client'
import type { Vehicle, VehicleSearchParams } from '@/types'

export const vehicleService = {
  search: async (params: VehicleSearchParams) => {
    const response = await apiClient.get('/vehicles/search', { params })
    return response.data
  },

  getById: async (id: string): Promise<Vehicle> => {
    const response = await apiClient.get(`/vehicles/${id}`)
    return response.data
  },

  create: async (data: VehicleCreateData): Promise<Vehicle> => {
    const response = await apiClient.post('/vehicles', data)
    return response.data
  },
}
```

## Backend Architecture

### Service Architecture

#### Traditional Server Architecture

##### Controller/Route Organization

```
src/modules/
├── auth/
│   ├── auth.controller.ts
│   ├── auth.service.ts
│   ├── auth.middleware.ts
│   └── auth.routes.ts
├── vehicles/
│   ├── vehicles.controller.ts
│   ├── vehicles.service.ts
│   ├── vehicles.repository.ts
│   └── vehicles.routes.ts
├── bookings/
│   ├── bookings.controller.ts
│   ├── bookings.service.ts
│   ├── bookings.repository.ts
│   └── bookings.routes.ts
└── payments/
    ├── payments.controller.ts
    ├── payments.service.ts
    └── payments.routes.ts
```

##### Controller Template

```typescript
// src/modules/vehicles/vehicles.controller.ts
import { Request, Response, NextFunction } from 'express'
import { VehicleService } from './vehicles.service'
import { VehicleSearchParams, VehicleCreateData } from '@/types'
import { validateSearchParams, validateVehicleData } from './vehicles.validation'

export class VehicleController {
  constructor(private vehicleService: VehicleService) {}

  search = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const params = validateSearchParams(req.query) as VehicleSearchParams
      const result = await this.vehicleService.searchVehicles(params)

      res.json({
        success: true,
        data: result,
        timestamp: new Date().toISOString(),
      })
    } catch (error) {
      next(error)
    }
  }

  create = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const userId = req.user!.id
      const vehicleData = validateVehicleData(req.body) as VehicleCreateData

      const vehicle = await this.vehicleService.createVehicle(userId, vehicleData)

      res.status(201).json({
        success: true,
        data: vehicle,
        timestamp: new Date().toISOString(),
      })
    } catch (error) {
      next(error)
    }
  }

  getById = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const { id } = req.params
      const vehicle = await this.vehicleService.getVehicleById(id)

      if (!vehicle) {
        return res.status(404).json({
          success: false,
          error: {
            code: 'VEHICLE_NOT_FOUND',
            message: '차량을 찾을 수 없습니다.',
          },
        })
      }

      res.json({
        success: true,
        data: vehicle,
        timestamp: new Date().toISOString(),
      })
    } catch (error) {
      next(error)
    }
  }
}
```

### Database Architecture

#### Schema Design

The database schema is designed for optimal performance with Korean market requirements:

- **UUID Primary Keys**: Better for distributed systems and privacy
- **Timezone Awareness**: All timestamps use Korean timezone (Asia/Seoul)
- **JSON Fields**: Flexible storage for images, ratings, and metadata
- **Proper Indexing**: Optimized for geospatial queries and search
- **Korean Text Support**: Full-text search with Korean language support

#### Data Access Layer

```typescript
// src/modules/vehicles/vehicles.repository.ts
import { PrismaClient } from '@prisma/client'
import type { Vehicle, VehicleSearchParams } from '@/types'

export class VehicleRepository {
  constructor(private prisma: PrismaClient) {}

  async searchVehicles(params: VehicleSearchParams): Promise<Vehicle[]> {
    const {
      lat,
      lng,
      start_datetime,
      end_datetime,
      radius = 5,
      fuel_type,
      price_min,
      price_max,
    } = params

    return this.prisma.vehicle.findMany({
      where: {
        status: 'active',
        // Geospatial query for radius
        AND: [
          {
            location_lat: {
              gte: lat - radius / 111, // Rough conversion to degrees
              lte: lat + radius / 111,
            },
          },
          {
            location_lng: {
              gte: lng - radius / (111 * Math.cos(lat * Math.PI / 180)),
              lte: lng + radius / (111 * Math.cos(lat * Math.PI / 180)),
            },
          },
        ],
        // Availability check
        NOT: {
          bookings: {
            some: {
              OR: [
                {
                  AND: [
                    { start_datetime: { lte: start_datetime } },
                    { end_datetime: { gt: start_datetime } },
                  ],
                },
                {
                  AND: [
                    { start_datetime: { lt: end_datetime } },
                    { end_datetime: { gte: end_datetime } },
                  ],
                },
              ],
              status: {
                in: ['approved', 'active'],
              },
            },
          },
        },
        // Price filter
        ...(price_min && { hourly_rate: { gte: price_min } }),
        ...(price_max && { hourly_rate: { lte: price_max } }),
        // Fuel type filter
        ...(fuel_type && { fuel_type: { in: fuel_type } }),
      },
      include: {
        owner: {
          select: {
            id: true,
            name: true,
            profile_image_url: true,
            is_verified: true,
          },
        },
      },
      orderBy: [
        { created_at: 'desc' },
      ],
    })
  }

  async createVehicle(ownerId: string, data: VehicleCreateData): Promise<Vehicle> {
    return this.prisma.vehicle.create({
      data: {
        ...data,
        owner_id: ownerId,
      },
      include: {
        owner: true,
      },
    })
  }
}
```

### Authentication and Authorization

#### Auth Flow

```mermaid
sequenceDiagram
    participant U as User
    participant FE as Frontend
    participant BE as Backend
    participant DB as Database
    participant REDIS as Redis

    U->>FE: Login request
    FE->>BE: POST /auth/login
    BE->>DB: Verify credentials
    DB->>BE: User data
    BE->>BE: Generate JWT tokens
    BE->>REDIS: Store refresh token
    BE->>FE: Access + Refresh tokens
    FE->>FE: Store tokens securely

    Note over FE,BE: Authenticated requests
    FE->>BE: API call with Bearer token
    BE->>BE: Verify JWT signature
    BE->>BE: Check token expiry
    BE->>FE: API response

    Note over FE,BE: Token refresh
    FE->>BE: POST /auth/refresh
    BE->>REDIS: Verify refresh token
    BE->>BE: Generate new access token
    BE->>FE: New access token
```

#### Middleware/Guards

```typescript
// src/shared/middleware/auth.middleware.ts
import { Request, Response, NextFunction } from 'express'
import jwt from 'jsonwebtoken'
import { User } from '@/types'
import { userService } from '@/modules/users/users.service'

interface AuthenticatedRequest extends Request {
  user?: User
}

export const authenticateToken = async (
  req: AuthenticatedRequest,
  res: Response,
  next: NextFunction
) => {
  const authHeader = req.headers['authorization']
  const token = authHeader && authHeader.split(' ')[1]

  if (!token) {
    return res.status(401).json({
      success: false,
      error: {
        code: 'MISSING_TOKEN',
        message: '인증 토큰이 필요합니다.',
      },
    })
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET!) as { userId: string }
    const user = await userService.findById(decoded.userId)

    if (!user) {
      return res.status(401).json({
        success: false,
        error: {
          code: 'INVALID_TOKEN',
          message: '유효하지 않은 토큰입니다.',
        },
      })
    }

    req.user = user
    next()
  } catch (error) {
    return res.status(403).json({
      success: false,
      error: {
        code: 'TOKEN_EXPIRED',
        message: '토큰이 만료되었습니다.',
      },
    })
  }
}

export const requireRole = (roles: string[]) => {
  return (req: AuthenticatedRequest, res: Response, next: NextFunction) => {
    if (!req.user || !roles.includes(req.user.role)) {
      return res.status(403).json({
        success: false,
        error: {
          code: 'INSUFFICIENT_PERMISSIONS',
          message: '권한이 부족합니다.',
        },
      })
    }
    next()
  }
}
```

## Unified Project Structure

```
carshare-platform/
├── .github/                        # CI/CD workflows
│   └── workflows/
│       ├── ci.yaml                 # Test and lint
│       ├── deploy-staging.yaml     # Staging deployment
│       └── deploy-production.yaml  # Production deployment
├── apps/                           # Application packages
│   ├── web/                        # Next.js frontend application
│   │   ├── src/
│   │   │   ├── app/                # Next.js 14 app directory
│   │   │   │   ├── (auth)/         # Auth route group
│   │   │   │   ├── (dashboard)/    # Protected dashboard
│   │   │   │   ├── vehicles/       # Public vehicle pages
│   │   │   │   ├── layout.tsx      # Root layout
│   │   │   │   ├── page.tsx        # Home page
│   │   │   │   └── globals.css     # Global styles
│   │   │   ├── components/         # React components
│   │   │   │   ├── ui/             # Base UI components
│   │   │   │   ├── features/       # Feature components
│   │   │   │   ├── layout/         # Layout components
│   │   │   │   └── common/         # Common components
│   │   │   ├── hooks/              # Custom React hooks
│   │   │   ├── lib/                # Frontend utilities
│   │   │   │   ├── api/            # API client services
│   │   │   │   ├── stores/         # Zustand stores
│   │   │   │   ├── utils/          # Utility functions
│   │   │   │   └── config/         # Configuration
│   │   │   └── types/              # Frontend-specific types
│   │   ├── public/                 # Static assets
│   │   ├── tests/                  # Frontend tests
│   │   ├── next.config.js          # Next.js configuration
│   │   ├── tailwind.config.js      # Tailwind configuration
│   │   ├── package.json            # Frontend dependencies
│   │   └── tsconfig.json           # TypeScript config
│   └── api/                        # Express.js backend application
│       ├── src/
│       │   ├── modules/            # Business modules
│       │   │   ├── auth/           # Authentication module
│       │   │   ├── users/          # User management
│       │   │   ├── vehicles/       # Vehicle management
│       │   │   ├── bookings/       # Booking lifecycle
│       │   │   ├── payments/       # Payment processing
│       │   │   └── notifications/  # Real-time notifications
│       │   ├── shared/             # Shared backend utilities
│       │   │   ├── database/       # Database configuration
│       │   │   ├── external/       # External API clients
│       │   │   ├── middleware/     # Express middleware
│       │   │   ├── utils/          # Backend utilities
│       │   │   └── types/          # Backend-specific types
│       │   ├── app.ts              # Express app setup
│       │   ├── server.ts           # Server startup
│       │   └── routes.ts           # Route definitions
│       ├── tests/                  # Backend tests
│       │   ├── unit/               # Unit tests
│       │   ├── integration/        # Integration tests
│       │   └── fixtures/           # Test data
│       ├── prisma/                 # Database schema
│       │   ├── schema.prisma       # Prisma schema
│       │   ├── migrations/         # Database migrations
│       │   └── seed.ts             # Database seeding
│       ├── Dockerfile              # Container definition
│       ├── package.json            # Backend dependencies
│       └── tsconfig.json           # TypeScript config
├── packages/                       # Shared packages
│   ├── shared/                     # Shared types and utilities
│   │   ├── src/
│   │   │   ├── types/              # Shared TypeScript interfaces
│   │   │   │   ├── auth.ts         # Authentication types
│   │   │   │   ├── vehicle.ts      # Vehicle types
│   │   │   │   ├── booking.ts      # Booking types
│   │   │   │   ├── payment.ts      # Payment types
│   │   │   │   ├── user.ts         # User types
│   │   │   │   └── api.ts          # API response types
│   │   │   ├── constants/          # Shared constants
│   │   │   │   ├── roles.ts        # User roles
│   │   │   │   ├── status.ts       # Status enums
│   │   │   │   └── regions.ts      # Korean regions
│   │   │   ├── utils/              # Shared utilities
│   │   │   │   ├── validation.ts   # Joi schemas
│   │   │   │   ├── format.ts       # Formatting functions
│   │   │   │   └── date.ts         # Date utilities
│   │   │   └── index.ts            # Package exports
│   │   ├── package.json            # Shared package config
│   │   └── tsconfig.json           # TypeScript config
│   ├── ui/                         # Shared UI components (for future mobile)
│   │   ├── src/
│   │   │   ├── components/         # Reusable components
│   │   │   ├── styles/             # Shared styles
│   │   │   └── icons/              # Icon components
│   │   ├── package.json            # UI package config
│   │   └── tsconfig.json           # TypeScript config
│   └── config/                     # Shared configuration
│       ├── eslint/                 # ESLint configurations
│       │   ├── base.js             # Base ESLint config
│       │   ├── react.js            # React-specific rules
│       │   └── node.js             # Node.js-specific rules
│       ├── typescript/             # TypeScript configurations
│       │   ├── base.json           # Base TS config
│       │   ├── react.json          # React TS config
│       │   └── node.json           # Node.js TS config
│       └── jest/                   # Jest configurations
│           ├── base.js             # Base Jest config
│           ├── react.js            # React testing config
│           └── node.js             # Node.js testing config
├── infrastructure/                 # Infrastructure as Code
│   ├── aws-cdk/                    # AWS CDK definitions
│   │   ├── lib/                    # CDK stack definitions
│   │   │   ├── network-stack.ts    # VPC, subnets, security groups
│   │   │   ├── database-stack.ts   # RDS PostgreSQL, ElastiCache
│   │   │   ├── storage-stack.ts    # S3 buckets, CloudFront
│   │   │   ├── compute-stack.ts    # ECS Fargate, ALB
│   │   │   └── monitoring-stack.ts # CloudWatch, alerts
│   │   ├── bin/                    # CDK app entry points
│   │   ├── cdk.json                # CDK configuration
│   │   └── package.json            # CDK dependencies
│   ├── docker/                     # Docker configurations
│   │   ├── frontend.Dockerfile     # Frontend container
│   │   ├── backend.Dockerfile      # Backend container
│   │   └── docker-compose.yml      # Local development
│   └── kubernetes/                 # K8s manifests (future)
├── scripts/                        # Build and deployment scripts
│   ├── setup.sh                    # Initial project setup
│   ├── build.sh                    # Build all applications
│   ├── deploy.sh                   # Deployment script
│   ├── test.sh                     # Run all tests
│   └── migrate.sh                  # Database migration
├── docs/                           # Project documentation
│   ├── prd.md                      # Product Requirements Document
│   ├── front-end-spec.md           # Frontend UX/UI Specification
│   ├── market-research.md          # Market Research Report
│   ├── brief.md                    # Project Brief
│   ├── backend-architecture.md     # Backend Architecture
│   ├── frontend-architecture.md    # Frontend Architecture
│   ├── fullstack-architecture.md   # This document
│   └── api/                        # API documentation
│       ├── openapi.yaml            # OpenAPI specification
│       └── postman/                # Postman collections
├── .env.example                    # Environment variables template
├── .gitignore                      # Git ignore patterns
├── package.json                    # Root package.json with workspaces
├── package-lock.json               # Lock file
├── lerna.json                      # Lerna configuration (optional)
├── turbo.json                      # Turborepo configuration (optional)
├── README.md                       # Project overview
└── CHANGELOG.md                    # Version history
```

## Development Workflow

### Local Development Setup

#### Prerequisites

```bash
# Install required tools
node --version  # v20.11.0 or higher
npm --version   # 10.2.0 or higher
docker --version  # 24.0.0 or higher
aws-cli --version  # 2.15.0 or higher

# Install global dependencies
npm install -g typescript@5.3.3
npm install -g @aws-cdk/cli@2.100.0
npm install -g prisma@5.7.1
```

#### Initial Setup

```bash
# Clone repository
git clone https://github.com/company/carshare-platform.git
cd carshare-platform

# Install all dependencies
npm install

# Setup environment variables
cp .env.example .env.local
# Edit .env.local with your configuration

# Setup database
docker-compose up -d postgres redis
npx prisma migrate dev
npx prisma db seed

# Build shared packages
npm run build:shared

# Start development servers
npm run dev
```

#### Development Commands

```bash
# Start all services
npm run dev

# Start frontend only (runs on http://localhost:3000)
npm run dev:frontend

# Start backend only (runs on http://localhost:3001)
npm run dev:backend

# Run tests
npm test                    # All tests
npm run test:frontend       # Frontend tests only
npm run test:backend        # Backend tests only
npm run test:e2e           # End-to-end tests
npm run test:coverage      # Coverage report

# Database operations
npm run db:migrate         # Run migrations
npm run db:seed           # Seed database
npm run db:reset          # Reset and seed
npm run db:studio         # Open Prisma Studio

# Code quality
npm run lint              # Lint all code
npm run lint:fix          # Fix linting issues
npm run type-check        # TypeScript checking
npm run format            # Format code with Prettier

# Build for production
npm run build             # Build all applications
npm run build:frontend    # Build frontend only
npm run build:backend     # Build backend only
```

### Environment Configuration

#### Required Environment Variables

```bash
# Frontend (.env.local)
NEXT_PUBLIC_API_URL=http://localhost:3001/api/v1
NEXT_PUBLIC_WS_URL=ws://localhost:3001
NEXT_PUBLIC_KAKAO_MAP_API_KEY=your_kakao_map_api_key
NEXT_PUBLIC_TOSS_PAYMENTS_CLIENT_KEY=your_toss_payments_client_key
NEXT_PUBLIC_ENABLE_REAL_TIME=true
NEXT_PUBLIC_GA_TRACKING_ID=your_google_analytics_id
NEXT_PUBLIC_SENTRY_DSN=your_sentry_dsn

# Backend (.env)
NODE_ENV=development
PORT=3001
DATABASE_URL=postgresql://carshare:password@localhost:5432/carshare_dev
REDIS_URL=redis://localhost:6379
JWT_SECRET=your_jwt_secret_here
JWT_REFRESH_SECRET=your_jwt_refresh_secret_here
AWS_ACCESS_KEY_ID=your_aws_access_key
AWS_SECRET_ACCESS_KEY=your_aws_secret_key
AWS_REGION=ap-northeast-2
S3_BUCKET_NAME=carshare-images-dev
KAKAO_MAP_API_KEY=your_kakao_map_api_key
TOSS_PAYMENTS_SECRET_KEY=your_toss_payments_secret_key
FIREBASE_PROJECT_ID=your_firebase_project_id
FIREBASE_PRIVATE_KEY=your_firebase_private_key
FIREBASE_CLIENT_EMAIL=your_firebase_client_email

# Shared
DATABASE_URL=postgresql://carshare:password@localhost:5432/carshare_dev
SENTRY_DSN=your_sentry_dsn
LOG_LEVEL=debug
CORS_ORIGIN=http://localhost:3000
```

## Deployment Architecture

### Deployment Strategy

**Frontend Deployment:**
- **Platform**: AWS CloudFront + S3 Static Hosting
- **Build Command**: `npm run build:frontend`
- **Output Directory**: `apps/web/.next/out`
- **CDN/Edge**: CloudFront with Korean edge locations

**Backend Deployment:**
- **Platform**: AWS ECS Fargate
- **Build Command**: `npm run build:backend`
- **Deployment Method**: Blue-Green deployment with health checks

### CI/CD Pipeline

```yaml
# .github/workflows/deploy.yml
name: Deploy to AWS

on:
  push:
    branches: [main, staging]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Type check
        run: npm run type-check

      - name: Lint
        run: npm run lint

  build-and-deploy:
    needs: test
    runs-on: ubuntu-latest
    environment: ${{ github.ref == 'refs/heads/main' && 'production' || 'staging' }}

    steps:
      - uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v3
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-northeast-2

      - name: Build frontend
        run: |
          npm ci
          npm run build:frontend

      - name: Deploy frontend to S3
        run: |
          aws s3 sync apps/web/.next/out/ s3://${{ secrets.S3_BUCKET_NAME }}/ --delete
          aws cloudfront create-invalidation --distribution-id ${{ secrets.CLOUDFRONT_DISTRIBUTION_ID }} --paths "/*"

      - name: Build and push backend image
        run: |
          docker build -f infrastructure/docker/backend.Dockerfile -t carshare-api:${{ github.sha }} .
          docker tag carshare-api:${{ github.sha }} ${{ secrets.ECR_REGISTRY }}/carshare-api:${{ github.sha }}
          docker push ${{ secrets.ECR_REGISTRY }}/carshare-api:${{ github.sha }}

      - name: Deploy to ECS
        run: |
          aws ecs update-service --cluster carshare-cluster --service carshare-api-service --task-definition carshare-api:${{ github.sha }} --force-new-deployment
```

### Environments

| Environment | Frontend URL | Backend URL | Purpose |
|-------------|-------------|-------------|---------|
| Development | http://localhost:3000 | http://localhost:3001 | Local development |
| Staging | https://staging.carshare.co.kr | https://api-staging.carshare.co.kr | Pre-production testing |
| Production | https://carshare.co.kr | https://api.carshare.co.kr | Live environment |

## Security and Performance

### Security Requirements

**Frontend Security:**
- CSP Headers: `default-src 'self'; script-src 'self' 'unsafe-inline' *.kakao.com *.tosspayments.com; style-src 'self' 'unsafe-inline'`
- XSS Prevention: Input sanitization, React's built-in XSS protection
- Secure Storage: JWT tokens in httpOnly cookies, sensitive data in secure storage

**Backend Security:**
- Input Validation: Joi schemas for all inputs, SQL injection prevention
- Rate Limiting: 100 requests/minute per IP, 1000/hour per authenticated user
- CORS Policy: Specific origins allowed, credentials included for secure requests

**Authentication Security:**
- Token Storage: Access tokens in memory, refresh tokens in httpOnly cookies
- Session Management: JWT with 15-minute expiry, refresh token rotation
- Password Policy: Minimum 8 characters, special characters required, bcrypt hashing

### Performance Optimization

**Frontend Performance:**
- Bundle Size Target: < 500KB initial bundle, code splitting for routes
- Loading Strategy: Server-side rendering for SEO, progressive hydration
- Caching Strategy: CDN caching for static assets, SWR for API data

**Backend Performance:**
- Response Time Target: < 500ms for API calls, < 2s for complex queries
- Database Optimization: Proper indexing, query optimization, connection pooling
- Caching Strategy: Redis for session data, API response caching for 5 minutes

## Testing Strategy

### Testing Pyramid

```
    E2E Tests (10%)
    /              \
   Integration Tests (20%)
  /                      \
Frontend Unit (35%)  Backend Unit (35%)
```

### Test Organization

#### Frontend Tests

```
apps/web/tests/
├── components/              # Component unit tests
│   ├── ui/
│   └── features/
├── hooks/                   # Custom hook tests
├── pages/                   # Page integration tests
├── utils/                   # Utility function tests
└── e2e/                     # End-to-end tests
    ├── auth.spec.ts
    ├── booking.spec.ts
    └── vehicle-search.spec.ts
```

#### Backend Tests

```
apps/api/tests/
├── unit/                    # Unit tests
│   ├── services/
│   ├── repositories/
│   └── utils/
├── integration/             # API integration tests
│   ├── auth.test.ts
│   ├── vehicles.test.ts
│   └── bookings.test.ts
└── fixtures/                # Test data
    ├── users.ts
    ├── vehicles.ts
    └── bookings.ts
```

#### E2E Tests

```
tests/e2e/
├── auth/
│   ├── guest-registration.spec.ts
│   ├── host-registration.spec.ts
│   └── login-flow.spec.ts
├── booking/
│   ├── vehicle-search.spec.ts
│   ├── booking-creation.spec.ts
│   ├── host-approval.spec.ts
│   └── payment-flow.spec.ts
└── admin/
    └── user-management.spec.ts
```

### Test Examples

#### Frontend Component Test

```typescript
// apps/web/tests/components/features/vehicles/VehicleCard.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import { VehicleCard } from '@/components/features/vehicles/VehicleCard'
import { mockVehicle } from '../../../fixtures/vehicles'

describe('VehicleCard', () => {
  it('displays vehicle information correctly', () => {
    render(<VehicleCard vehicle={mockVehicle} />)

    expect(screen.getByText('Genesis G90')).toBeInTheDocument()
    expect(screen.getByText('2022년식')).toBeInTheDocument()
    expect(screen.getByText('시간당 15,000원')).toBeInTheDocument()
  })

  it('calls onClick when card is clicked', () => {
    const handleClick = jest.fn()
    render(<VehicleCard vehicle={mockVehicle} onClick={handleClick} />)

    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledWith(mockVehicle)
  })

  it('shows owner information when showOwner is true', () => {
    render(<VehicleCard vehicle={mockVehicle} showOwner />)

    expect(screen.getByText(mockVehicle.owner.name)).toBeInTheDocument()
  })
})
```

#### Backend API Test

```typescript
// apps/api/tests/integration/vehicles.test.ts
import request from 'supertest'
import { app } from '../../src/app'
import { createTestUser, createTestVehicle } from '../fixtures'

describe('Vehicles API', () => {
  let authToken: string
  let hostUser: any

  beforeEach(async () => {
    hostUser = await createTestUser({ role: 'host' })
    authToken = generateAuthToken(hostUser.id)
  })

  describe('POST /vehicles', () => {
    it('creates a new vehicle for authenticated host', async () => {
      const vehicleData = {
        license_plate: '12가3456',
        make: 'Genesis',
        model: 'G90',
        year: 2022,
        hourly_rate: 15000,
        location_address: '서울특별시 강남구 테헤란로 427',
      }

      const response = await request(app)
        .post('/api/v1/vehicles')
        .set('Authorization', `Bearer ${authToken}`)
        .send(vehicleData)

      expect(response.status).toBe(201)
      expect(response.body.success).toBe(true)
      expect(response.body.data.make).toBe('Genesis')
      expect(response.body.data.owner_id).toBe(hostUser.id)
    })

    it('returns 401 for unauthenticated requests', async () => {
      const response = await request(app)
        .post('/api/v1/vehicles')
        .send({})

      expect(response.status).toBe(401)
    })
  })

  describe('GET /vehicles/search', () => {
    it('returns vehicles within search radius', async () => {
      const vehicle = await createTestVehicle({ owner_id: hostUser.id })

      const response = await request(app)
        .get('/api/v1/vehicles/search')
        .query({
          lat: 37.5665,
          lng: 126.9780,
          start_datetime: '2024-01-20T10:00:00Z',
          end_datetime: '2024-01-20T18:00:00Z',
          radius: 10,
        })

      expect(response.status).toBe(200)
      expect(response.body.vehicles).toHaveLength(1)
      expect(response.body.vehicles[0].id).toBe(vehicle.id)
    })
  })
})
```

#### E2E Test

```typescript
// tests/e2e/booking/complete-booking-flow.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Complete Booking Flow', () => {
  test('guest can complete full booking process', async ({ page }) => {
    // Login as guest
    await page.goto('/login')
    await page.fill('[data-testid="email"]', 'guest@test.com')
    await page.fill('[data-testid="password"]', 'password123')
    await page.click('[data-testid="login-button"]')

    // Search for vehicles
    await page.goto('/vehicles/search')
    await page.fill('[data-testid="location-input"]', '강남구')
    await page.click('[data-testid="date-picker"]')
    await page.click('[data-testid="today"]')
    await page.click('[data-testid="search-button"]')

    // Select a vehicle
    await expect(page.locator('[data-testid="vehicle-card"]').first()).toBeVisible()
    await page.click('[data-testid="vehicle-card"]').first()

    // Make booking request
    await page.click('[data-testid="book-now-button"]')
    await page.fill('[data-testid="guest-message"]', '안전하게 이용하겠습니다.')
    await page.click('[data-testid="submit-booking"]')

    // Wait for booking confirmation
    await expect(page.locator('[data-testid="booking-pending"]')).toBeVisible()
    await expect(page.getByText('예약 신청이 완료되었습니다')).toBeVisible()

    // Check booking appears in my bookings
    await page.goto('/guest/bookings')
    await expect(page.locator('[data-testid="booking-item"]').first()).toBeVisible()
    await expect(page.getByText('승인 대기중')).toBeVisible()
  })
})
```

## Coding Standards

### Critical Fullstack Rules

- **Type Sharing**: Always define types in packages/shared and import from there - no duplicate type definitions between frontend and backend
- **API Calls**: Never make direct HTTP calls in components - use the service layer and custom hooks
- **Environment Variables**: Access only through config objects, never process.env directly in application code
- **Error Handling**: All API routes must use the standard error handler, frontend must handle all error states
- **State Updates**: Never mutate state directly - use proper state management patterns (Zustand actions)
- **Database Access**: Always use repository pattern, never direct Prisma queries in controllers or services
- **Authentication**: All protected routes/endpoints must use proper authentication middleware
- **Validation**: All inputs must be validated using shared Joi schemas from packages/shared
- **Real-time Updates**: Use WebSocket hooks for live data, implement proper fallback for connection failures
- **Korean Localization**: All user-facing text must support Korean language, use proper date/currency formatting

### Naming Conventions

| Element | Frontend | Backend | Example |
|---------|----------|---------|---------|
| Components | PascalCase | - | `VehicleCard.tsx` |
| Hooks | camelCase with 'use' | - | `useAuth.ts` |
| API Routes | - | kebab-case | `/api/vehicles/search` |
| Database Tables | - | snake_case | `vehicle_availabilities` |
| Environment Variables | UPPER_SNAKE_CASE | UPPER_SNAKE_CASE | `NEXT_PUBLIC_API_URL` |
| Types/Interfaces | PascalCase | PascalCase | `VehicleSearchParams` |
| Functions | camelCase | camelCase | `createBooking()` |
| Constants | UPPER_SNAKE_CASE | UPPER_SNAKE_CASE | `MAX_UPLOAD_SIZE` |

## Error Handling Strategy

### Error Flow

```mermaid
sequenceDiagram
    participant FE as Frontend
    participant BE as Backend
    participant DB as Database
    participant EXT as External API

    FE->>BE: API Request
    BE->>DB: Database Query
    DB-->>BE: Database Error
    BE->>BE: Log Error with Context
    BE->>BE: Transform to API Error
    BE->>FE: Structured Error Response
    FE->>FE: Display User-Friendly Message
    FE->>FE: Log to Error Tracking

    FE->>BE: API Request
    BE->>EXT: External API Call
    EXT-->>BE: External API Error
    BE->>BE: Retry Logic (if applicable)
    BE->>BE: Circuit Breaker Check
    BE->>BE: Fallback Response
    BE->>FE: Graceful Error Response
```

### Error Response Format

```typescript
interface ApiError {
  error: {
    code: string            // Machine-readable error code
    message: string         // User-friendly Korean message
    details?: Record<string, any>  // Additional error context
    timestamp: string       // ISO timestamp
    requestId: string       // Correlation ID for debugging
    field?: string          // For validation errors
  }
}

// Example error responses
{
  "error": {
    "code": "VEHICLE_NOT_AVAILABLE",
    "message": "선택하신 시간에 차량을 이용할 수 없습니다.",
    "details": {
      "vehicleId": "123e4567-e89b-12d3-a456-426614174000",
      "requestedTime": "2024-01-20T10:00:00Z"
    },
    "timestamp": "2024-01-19T14:30:00Z",
    "requestId": "req_123456789"
  }
}
```

### Frontend Error Handling

```typescript
// lib/utils/error-handler.ts
import { toast } from '@/components/ui/toast'
import { ApiError } from '@/types'

export class ErrorHandler {
  static handle(error: unknown) {
    if (this.isApiError(error)) {
      const apiError = error.response?.data?.error
      if (apiError) {
        toast.error(apiError.message)
        this.logError('API_ERROR', apiError)
        return
      }
    }

    if (this.isNetworkError(error)) {
      toast.error('네트워크 연결을 확인해주세요.')
      this.logError('NETWORK_ERROR', { message: error.message })
      return
    }

    // Fallback for unknown errors
    toast.error('알 수 없는 오류가 발생했습니다.')
    this.logError('UNKNOWN_ERROR', error)
  }

  static isApiError(error: any): boolean {
    return error?.response?.data?.error
  }

  static isNetworkError(error: any): boolean {
    return error?.code === 'NETWORK_ERROR' || !navigator.onLine
  }

  static logError(type: string, error: any) {
    // Send to error tracking service (Sentry)
    console.error(`[${type}]`, error)
  }
}

// Usage in components/hooks
try {
  await vehicleService.createVehicle(data)
} catch (error) {
  ErrorHandler.handle(error)
}
```

### Backend Error Handling

```typescript
// src/shared/middleware/error.middleware.ts
import { Request, Response, NextFunction } from 'express'
import { v4 as uuidv4 } from 'uuid'
import { logger } from '@/shared/utils/logger'

export interface AppError extends Error {
  statusCode: number
  code: string
  isOperational: boolean
}

export class APIError extends Error implements AppError {
  statusCode: number
  code: string
  isOperational: boolean

  constructor(message: string, statusCode: number = 500, code: string = 'INTERNAL_ERROR') {
    super(message)
    this.statusCode = statusCode
    this.code = code
    this.isOperational = true
    Error.captureStackTrace(this, this.constructor)
  }
}

export const errorHandler = (
  error: Error,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  const requestId = uuidv4()

  // Log error with context
  logger.error('API Error', {
    requestId,
    error: error.message,
    stack: error.stack,
    url: req.url,
    method: req.method,
    userId: req.user?.id,
    ip: req.ip,
  })

  // Handle different error types
  if (error instanceof APIError) {
    return res.status(error.statusCode).json({
      success: false,
      error: {
        code: error.code,
        message: error.message,
        timestamp: new Date().toISOString(),
        requestId,
      },
    })
  }

  // Handle Prisma errors
  if (error.name === 'PrismaClientKnownRequestError') {
    const prismaError = error as any
    if (prismaError.code === 'P2002') {
      return res.status(409).json({
        success: false,
        error: {
          code: 'DUPLICATE_ENTRY',
          message: '이미 존재하는 데이터입니다.',
          timestamp: new Date().toISOString(),
          requestId,
        },
      })
    }
  }

  // Handle validation errors
  if (error.name === 'ValidationError') {
    return res.status(400).json({
      success: false,
      error: {
        code: 'VALIDATION_ERROR',
        message: '입력값을 확인해주세요.',
        details: error.message,
        timestamp: new Date().toISOString(),
        requestId,
      },
    })
  }

  // Fallback for unknown errors
  res.status(500).json({
    success: false,
    error: {
      code: 'INTERNAL_ERROR',
      message: '서버 오류가 발생했습니다.',
      timestamp: new Date().toISOString(),
      requestId,
    },
  })
}

// Async error wrapper
export const asyncHandler = (fn: Function) => {
  return (req: Request, res: Response, next: NextFunction) => {
    Promise.resolve(fn(req, res, next)).catch(next)
  }
}
```

## Monitoring and Observability

### Monitoring Stack

- **Frontend Monitoring**: Sentry for error tracking, Google Analytics for user behavior
- **Backend Monitoring**: AWS CloudWatch for infrastructure, Sentry for application errors
- **Error Tracking**: Sentry with Korean localization and custom error context
- **Performance Monitoring**: CloudWatch metrics, custom dashboards for business KPIs

### Key Metrics

**Frontend Metrics:**
- Core Web Vitals (LCP, FID, CLS)
- JavaScript errors and crash rate
- API response times from client perspective
- User interactions and conversion funnel
- Bundle size and loading performance

**Backend Metrics:**
- Request rate and throughput
- Error rate by endpoint and user type
- Response time percentiles (p50, p95, p99)
- Database query performance
- External API response times
- WebSocket connection health

**Business Metrics:**
- Booking conversion rate
- Host registration funnel
- Payment success rate
- User satisfaction scores
- Revenue per user

## Next Steps

### Development Readiness

This fullstack architecture provides:
✅ **Complete development framework** with shared types and utilities
✅ **End-to-end type safety** from database to UI components
✅ **Real-time capabilities** with WebSocket integration
✅ **Korean market optimization** with local service integrations
✅ **Production-ready deployment** strategy with AWS infrastructure
✅ **Comprehensive testing** strategy across all layers
✅ **AI-friendly development** patterns for consistent code generation

### Implementation Roadmap

**Phase 1: Foundation (Week 1-2)**
1. Setup monorepo structure and shared packages
2. Implement authentication system (frontend + backend)
3. Setup CI/CD pipeline and development environment
4. Create base UI components and design system

**Phase 2: Core Features (Week 3-4)**
1. Implement vehicle management (registration, search, details)
2. Build booking workflow (creation, approval, management)
3. Integrate Korean services (Kakao Map, Toss Payments)
4. Setup real-time notifications

**Phase 3: Advanced Features (Week 5-6)**
1. Complete payment processing and settlement
2. Implement review and rating system
3. Add admin dashboard and reporting
4. Performance optimization and monitoring

**Phase 4: Production Launch (Week 7-8)**
1. Security audit and penetration testing
2. Load testing and performance tuning
3. User acceptance testing with Korean users
4. Production deployment and monitoring setup

### Team Assignments

**Frontend Developer**:
- Implement React components following the architecture patterns
- Integrate with Korean services (Kakao Map SDK, Toss Payments UI)
- Ensure mobile-responsive design for Korean users
- Implement real-time features with WebSocket hooks

**Backend Developer**:
- Build API endpoints following the OpenAPI specification
- Integrate with external Korean services (payment processing, maps)
- Implement real-time WebSocket server for notifications
- Setup monitoring and logging infrastructure

**DevOps Engineer**:
- Setup AWS infrastructure using provided CDK definitions
- Configure CI/CD pipeline for automated deployments
- Implement monitoring and alerting systems
- Setup database backup and disaster recovery procedures

**QA Engineer**:
- Implement automated testing following the testing strategy
- Create test data for Korean market scenarios
- Perform manual testing with Korean language support
- Setup performance and security testing frameworks