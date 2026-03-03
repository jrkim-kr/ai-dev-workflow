# Specification: Restaurant Reservation System

> **Version:** 0.1
> **Date:** 2026-03-03
> **Author:** AI Dev Workflow (Example)
> **Status:** Approved

---

## 1. Overview

A REST API for managing restaurant table reservations. Customers can create, view, modify, and cancel reservations. Restaurant staff can view all reservations and manage table availability. The system enforces capacity constraints and prevents double-booking.

---

## 2. Actors

| Actor | Role | Permissions |
|-------|------|-------------|
| Customer | End user making reservations | Create, view own, modify own, cancel own |
| Staff | Restaurant employee | View all, modify all, cancel all, manage tables |
| System | Automated processes | Send reminders, expire no-shows, generate reports |

---

## 3. Functional Requirements

### FR-001: Create Reservation

**Description:** Customers can create a reservation by specifying date, time, party size, and optional notes.
**Priority:** Must-have
**Acceptance Criteria:**
- [ ] Reservation created with unique confirmation code
- [ ] System validates table availability for the requested date/time/party size
- [ ] System rejects reservations when no suitable table is available
- [ ] Confirmation response includes reservation details and confirmation code

### FR-002: View Reservation

**Description:** Customers can view their reservation details using a confirmation code.
**Priority:** Must-have
**Acceptance Criteria:**
- [ ] Returns full reservation details for valid confirmation code
- [ ] Returns 404 for invalid confirmation code
- [ ] Does not expose other customers' reservations

### FR-003: Modify Reservation

**Description:** Customers can modify date, time, or party size of an existing reservation.
**Priority:** Must-have
**Acceptance Criteria:**
- [ ] Modification allowed up to 2 hours before reservation time
- [ ] System re-validates table availability for new parameters
- [ ] Confirmation code remains the same after modification
- [ ] Returns error if modification is not possible (no availability, too late)

### FR-004: Cancel Reservation

**Description:** Customers can cancel their reservation using the confirmation code.
**Priority:** Must-have
**Acceptance Criteria:**
- [ ] Cancellation allowed up to 1 hour before reservation time
- [ ] Cancelled reservation frees the table for other bookings
- [ ] Cancellation is idempotent (cancelling twice returns success)
- [ ] Returns error if cancellation window has passed

### FR-005: Staff Dashboard

**Description:** Staff can view all reservations for a given date, filtered by status.
**Priority:** Must-have
**Acceptance Criteria:**
- [ ] Returns all reservations for specified date
- [ ] Filterable by status: confirmed, cancelled, completed, no-show
- [ ] Sortable by time
- [ ] Includes customer name, party size, table assignment, and notes

### FR-006: Table Management

**Description:** Staff can add, remove, and modify table configurations.
**Priority:** Should-have
**Acceptance Criteria:**
- [ ] Tables have: number, capacity (min/max guests), location (indoor/outdoor)
- [ ] Cannot delete a table with future confirmed reservations
- [ ] Capacity changes validated against existing reservations

### FR-007: No-Show Handling

**Description:** System automatically marks reservations as no-show if not checked in within 30 minutes of reservation time.
**Priority:** Should-have
**Acceptance Criteria:**
- [ ] Reservation status changes to "no-show" after 30-minute window
- [ ] Table becomes available for walk-ins after no-show is triggered
- [ ] No-show count is tracked per customer phone number

---

## 4. Non-Functional Requirements

### NFR-001: Performance

- API response time < 200ms at p95 under 100 concurrent users
- Reservation creation must complete within 500ms including availability check

### NFR-002: Security

- Staff endpoints require JWT authentication
- Customer endpoints use confirmation code as bearer token
- Rate limiting: 10 requests per minute per IP for reservation creation
- No PII in application logs

### NFR-003: Data Integrity

- No double-booking: a table cannot have overlapping confirmed reservations
- Reservation times are stored in UTC with timezone offset
- All timestamps include timezone information

---

## 5. Data Model

### Entity: Table

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| id | UUID | PK | Unique identifier |
| number | integer | UNIQUE, NOT NULL | Table display number |
| min_capacity | integer | NOT NULL, >= 1 | Minimum party size |
| max_capacity | integer | NOT NULL, >= min_capacity | Maximum party size |
| location | enum | indoor, outdoor | Table location |
| is_active | boolean | NOT NULL, default true | Whether table is available |
| created_at | timestamp | NOT NULL | Creation time |

### Entity: Reservation

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| id | UUID | PK | Unique identifier |
| confirmation_code | string(8) | UNIQUE, NOT NULL | Customer-facing code |
| table_id | UUID | FK → Table.id, NOT NULL | Assigned table |
| customer_name | string(100) | NOT NULL | Customer name |
| customer_phone | string(20) | NOT NULL | Customer phone |
| customer_email | string(255) | nullable | Customer email |
| party_size | integer | NOT NULL, >= 1 | Number of guests |
| reservation_time | timestamp | NOT NULL | Reservation date/time (UTC) |
| duration_minutes | integer | NOT NULL, default 90 | Expected duration |
| status | enum | confirmed, cancelled, completed, no_show | Current status |
| notes | text | nullable | Special requests |
| created_at | timestamp | NOT NULL | Creation time |
| updated_at | timestamp | NOT NULL | Last update time |

### Relationships

- Table has many Reservations
- Reservation belongs to one Table

---

## 6. API Contracts

### POST /api/reservations

**Description:** Create a new reservation
**Authentication:** Public
**Rate Limit:** 10/min per IP

**Request:**
```json
{
  "customer_name": "string (required)",
  "customer_phone": "string (required)",
  "customer_email": "string (optional)",
  "party_size": "integer (required, >= 1)",
  "reservation_time": "ISO 8601 datetime (required)",
  "duration_minutes": "integer (optional, default 90)",
  "notes": "string (optional)"
}
```

**Response (201):**
```json
{
  "confirmation_code": "ABC12345",
  "table_number": 5,
  "reservation_time": "2026-03-15T19:00:00Z",
  "party_size": 4,
  "status": "confirmed"
}
```

**Error Responses:**
| Status | Code | Description |
|--------|------|-------------|
| 400 | VALIDATION_ERROR | Invalid input (missing fields, invalid format) |
| 409 | NO_AVAILABILITY | No table available for requested parameters |
| 429 | RATE_LIMITED | Too many requests |

### GET /api/reservations/:confirmationCode

**Description:** View reservation details
**Authentication:** Public (confirmation code acts as access token)

**Response (200):**
```json
{
  "confirmation_code": "ABC12345",
  "customer_name": "Jane Doe",
  "table_number": 5,
  "party_size": 4,
  "reservation_time": "2026-03-15T19:00:00Z",
  "duration_minutes": 90,
  "status": "confirmed",
  "notes": "Window seat preferred"
}
```

**Error Responses:**
| Status | Code | Description |
|--------|------|-------------|
| 404 | NOT_FOUND | No reservation with this confirmation code |

### PATCH /api/reservations/:confirmationCode

**Description:** Modify an existing reservation
**Authentication:** Public (confirmation code acts as access token)

**Request:**
```json
{
  "party_size": "integer (optional)",
  "reservation_time": "ISO 8601 datetime (optional)",
  "notes": "string (optional)"
}
```

**Response (200):** Updated reservation object (same as GET response)

**Error Responses:**
| Status | Code | Description |
|--------|------|-------------|
| 400 | MODIFICATION_WINDOW_CLOSED | Less than 2 hours before reservation |
| 404 | NOT_FOUND | No reservation with this confirmation code |
| 409 | NO_AVAILABILITY | No table available for new parameters |

### DELETE /api/reservations/:confirmationCode

**Description:** Cancel a reservation
**Authentication:** Public (confirmation code acts as access token)

**Response (200):**
```json
{
  "confirmation_code": "ABC12345",
  "status": "cancelled"
}
```

**Error Responses:**
| Status | Code | Description |
|--------|------|-------------|
| 400 | CANCELLATION_WINDOW_CLOSED | Less than 1 hour before reservation |
| 404 | NOT_FOUND | No reservation with this confirmation code |

### GET /api/staff/reservations?date=YYYY-MM-DD&status=confirmed

**Description:** View all reservations for a date
**Authentication:** JWT (staff only)

**Response (200):**
```json
{
  "date": "2026-03-15",
  "reservations": [
    {
      "confirmation_code": "ABC12345",
      "customer_name": "Jane Doe",
      "customer_phone": "+1-555-0100",
      "table_number": 5,
      "party_size": 4,
      "reservation_time": "2026-03-15T19:00:00Z",
      "status": "confirmed",
      "notes": "Window seat preferred"
    }
  ],
  "total": 1
}
```

---

## 7. Acceptance Criteria Summary

| ID | Requirement | Key Criterion | Testable |
|----|------------|---------------|----------|
| FR-001 | Create reservation | Unique code generated, availability validated | Yes |
| FR-002 | View reservation | Returns details for valid code, 404 for invalid | Yes |
| FR-003 | Modify reservation | Re-validates availability, respects time window | Yes |
| FR-004 | Cancel reservation | Frees table, respects time window, idempotent | Yes |
| FR-005 | Staff dashboard | Returns all reservations, filterable, sortable | Yes |
| FR-006 | Table management | CRUD tables, validates against reservations | Yes |
| FR-007 | No-show handling | Auto-marks after 30 min, frees table | Yes |

---

## 8. Out of Scope

- Online payment processing
- Customer accounts and login
- Email/SMS notification delivery (API hooks only, no actual sending)
- Multi-restaurant support
- Waitlist management
- Table layout visualization
- Reservation analytics and reporting

---

## 9. Open Questions

| # | Question | Proposed Answer | Status |
|---|----------|----------------|--------|
| 1 | Should party size of 1 be allowed? | Yes, single diners are valid | Resolved |
| 2 | Maximum party size limit? | Capped at largest table max_capacity | Resolved |
| 3 | How far in advance can reservations be made? | Up to 30 days | Resolved |

---

## Approval

| Role | Name | Date | Decision |
|------|------|------|----------|
| Author | AI Dev Workflow | 2026-03-03 | Draft |
| Reviewer | — | — | Approved (example) |
