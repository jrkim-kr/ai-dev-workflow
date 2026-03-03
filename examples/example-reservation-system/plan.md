# Implementation Plan: Restaurant Reservation System

> **Version:** 0.1
> **Date:** 2026-03-03
> **Spec Reference:** spec.md v0.1
> **Status:** Approved

---

## 1. Architecture Overview

Layered REST API with clear separation between routing, business logic, and data access.

```
┌─────────────────────────────────────────┐
│              HTTP Layer                  │
│  Express.js routes + middleware          │
│  ├── /api/reservations (public)         │
│  └── /api/staff/* (JWT-protected)       │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│           Service Layer                  │
│  Business logic + validation             │
│  ├── ReservationService                 │
│  ├── TableService                       │
│  └── AvailabilityService               │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│            Data Layer                    │
│  Prisma ORM + PostgreSQL                │
│  ├── Reservation model                  │
│  └── Table model                        │
└─────────────────────────────────────────┘
```

---

## 2. Technology Stack

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Runtime | Node.js 20 LTS | Stable, async-first, wide ecosystem |
| Framework | Express.js 4 | Minimal, well-understood, flexible |
| Database | PostgreSQL 16 | ACID compliance for reservation integrity |
| ORM | Prisma 5 | Type-safe queries, migration management |
| Validation | Zod | Runtime schema validation, TypeScript inference |
| Auth | jsonwebtoken | Standard JWT implementation for staff auth |
| Testing | Vitest | Fast, ESM-native, compatible with TypeScript |
| Language | TypeScript 5 | Type safety across all layers |

---

## 3. File Structure

```
reservation-system/
├── src/
│   ├── routes/
│   │   ├── reservations.ts    # Public reservation endpoints
│   │   └── staff.ts           # Staff-only endpoints
│   ├── services/
│   │   ├── reservation.ts     # Reservation business logic
│   │   ├── table.ts           # Table management logic
│   │   └── availability.ts    # Availability checking logic
│   ├── middleware/
│   │   ├── auth.ts            # JWT authentication
│   │   ├── rate-limit.ts      # Rate limiting
│   │   └── error-handler.ts   # Global error handling
│   ├── schemas/
│   │   └── reservation.ts     # Zod validation schemas
│   ├── utils/
│   │   └── confirmation-code.ts  # Code generation
│   ├── config/
│   │   └── index.ts           # Environment configuration
│   └── app.ts                 # Express app setup
├── prisma/
│   ├── schema.prisma          # Database schema
│   └── migrations/            # Database migrations
├── tests/
│   ├── unit/
│   │   ├── availability.test.ts
│   │   ├── reservation.test.ts
│   │   └── confirmation-code.test.ts
│   ├── integration/
│   │   ├── create-reservation.test.ts
│   │   ├── modify-reservation.test.ts
│   │   └── staff-dashboard.test.ts
│   └── e2e/
│       └── reservation-flow.test.ts
├── package.json
├── tsconfig.json
└── .env.example
```

---

## 4. Component Design

### AvailabilityService

**Responsibility:** Determine if a table is available for a given time/party size.
**Dependencies:** Prisma client
**Interface:**
```typescript
class AvailabilityService {
  findAvailableTable(params: {
    partySize: number;
    reservationTime: Date;
    durationMinutes: number;
    excludeReservationId?: string;
  }): Promise<Table | null>;

  isTableAvailable(params: {
    tableId: string;
    reservationTime: Date;
    durationMinutes: number;
    excludeReservationId?: string;
  }): Promise<boolean>;
}
```
**Spec Requirements Covered:** FR-001 (availability check), FR-003 (re-validation), NFR-003 (no double-booking)

### ReservationService

**Responsibility:** CRUD operations for reservations with business rule enforcement.
**Dependencies:** AvailabilityService, Prisma client
**Interface:**
```typescript
class ReservationService {
  create(input: CreateReservationInput): Promise<Reservation>;
  findByConfirmationCode(code: string): Promise<Reservation | null>;
  modify(code: string, input: ModifyReservationInput): Promise<Reservation>;
  cancel(code: string): Promise<Reservation>;
  findByDate(date: string, status?: ReservationStatus): Promise<Reservation[]>;
}
```
**Spec Requirements Covered:** FR-001 through FR-005, FR-007

### TableService

**Responsibility:** Table configuration management.
**Dependencies:** Prisma client
**Interface:**
```typescript
class TableService {
  create(input: CreateTableInput): Promise<Table>;
  update(id: string, input: UpdateTableInput): Promise<Table>;
  delete(id: string): Promise<void>;
  list(): Promise<Table[]>;
}
```
**Spec Requirements Covered:** FR-006

---

## 5. Database Schema

### Migration: 001_initial_schema

```sql
CREATE TYPE table_location AS ENUM ('indoor', 'outdoor');
CREATE TYPE reservation_status AS ENUM ('confirmed', 'cancelled', 'completed', 'no_show');

CREATE TABLE tables (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  number INTEGER UNIQUE NOT NULL,
  min_capacity INTEGER NOT NULL CHECK (min_capacity >= 1),
  max_capacity INTEGER NOT NULL CHECK (max_capacity >= min_capacity),
  location table_location NOT NULL DEFAULT 'indoor',
  is_active BOOLEAN NOT NULL DEFAULT true,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE reservations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  confirmation_code VARCHAR(8) UNIQUE NOT NULL,
  table_id UUID NOT NULL REFERENCES tables(id),
  customer_name VARCHAR(100) NOT NULL,
  customer_phone VARCHAR(20) NOT NULL,
  customer_email VARCHAR(255),
  party_size INTEGER NOT NULL CHECK (party_size >= 1),
  reservation_time TIMESTAMPTZ NOT NULL,
  duration_minutes INTEGER NOT NULL DEFAULT 90,
  status reservation_status NOT NULL DEFAULT 'confirmed',
  notes TEXT,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_reservations_time ON reservations(reservation_time);
CREATE INDEX idx_reservations_status ON reservations(status);
CREATE INDEX idx_reservations_confirmation ON reservations(confirmation_code);
CREATE INDEX idx_reservations_table_time ON reservations(table_id, reservation_time);
```

---

## 6. Implementation Tasks

### Phase A: Foundation

| # | Task | Spec Ref | Dependencies | Complexity |
|---|------|----------|-------------|------------|
| A1 | Initialize project (package.json, tsconfig, Prisma) | — | None | Low |
| A2 | Define database schema in Prisma | FR-001, FR-006 | A1 | Low |
| A3 | Set up Express app with middleware | NFR-002 | A1 | Low |
| A4 | Implement configuration module | — | A1 | Low |
| A5 | Implement error handling middleware | — | A3 | Low |

### Phase B: Core Features

| # | Task | Spec Ref | Dependencies | Complexity |
|---|------|----------|-------------|------------|
| B1 | Implement AvailabilityService | FR-001, NFR-003 | A2 | High |
| B2 | Implement confirmation code generator | FR-001 | A1 | Low |
| B3 | Implement ReservationService.create | FR-001 | B1, B2 | Medium |
| B4 | Implement ReservationService.findByConfirmationCode | FR-002 | A2 | Low |
| B5 | Implement ReservationService.modify | FR-003 | B1 | Medium |
| B6 | Implement ReservationService.cancel | FR-004 | A2 | Low |
| B7 | Implement public reservation routes | FR-001-004 | B3-B6 | Medium |

### Phase C: Staff & Advanced Features

| # | Task | Spec Ref | Dependencies | Complexity |
|---|------|----------|-------------|------------|
| C1 | Implement JWT authentication middleware | NFR-002 | A3 | Medium |
| C2 | Implement staff dashboard endpoint | FR-005 | C1 | Low |
| C3 | Implement TableService | FR-006 | A2 | Low |
| C4 | Implement staff table management routes | FR-006 | C1, C3 | Low |
| C5 | Implement rate limiting middleware | NFR-002 | A3 | Low |
| C6 | Implement Zod validation schemas | FR-001-006 | A1 | Medium |

### Phase D: Automation

| # | Task | Spec Ref | Dependencies | Complexity |
|---|------|----------|-------------|------------|
| D1 | Implement no-show detection job | FR-007 | B3 | Medium |

---

## 7. Risk Assessment

| Risk | Impact | Likelihood | Mitigation |
|------|--------|-----------|------------|
| Double-booking race condition | High | Medium | Database-level constraint + transaction isolation |
| Timezone handling bugs | Medium | High | Store all times as UTC, validate timezone on input |
| Confirmation code collision | Low | Low | Use crypto-random + check uniqueness before insert |

---

## 8. Open Decisions

| # | Decision | Options | Recommendation | Status |
|---|----------|---------|---------------|--------|
| 1 | Confirmation code format | 8 alphanumeric / 6 digits / UUID prefix | 8 alphanumeric (readable, sufficient entropy) | Resolved |
| 2 | No-show job trigger | Cron job / On-demand check | On-demand check on dashboard load + periodic cron | Resolved |

---

## Approval

| Role | Name | Date | Decision |
|------|------|------|----------|
| Author | AI Dev Workflow | 2026-03-03 | Complete |
| Reviewer | — | — | Approved (example) |
