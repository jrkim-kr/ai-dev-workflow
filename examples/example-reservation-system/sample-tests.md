# Sample Tests: Restaurant Reservation System

This document shows example test cases mapped to specification requirements. These are representative — a complete test suite would include more edge cases.

---

## Test Matrix

| Spec Requirement | Test Type | Test Description | Status |
|-----------------|-----------|------------------|--------|
| FR-001 | Unit | Create reservation with valid input | Pass |
| FR-001 | Unit | Reject reservation when no table available | Pass |
| FR-001 | Unit | Generate unique confirmation code | Pass |
| FR-001 | Integration | Full creation flow with DB persistence | Pass |
| FR-002 | Unit | Retrieve reservation by confirmation code | Pass |
| FR-002 | Unit | Return null for invalid confirmation code | Pass |
| FR-003 | Unit | Modify reservation within time window | Pass |
| FR-003 | Unit | Reject modification outside time window | Pass |
| FR-003 | Unit | Re-validate availability on modification | Pass |
| FR-004 | Unit | Cancel reservation within time window | Pass |
| FR-004 | Unit | Reject cancellation outside time window | Pass |
| FR-004 | Unit | Cancellation is idempotent | Pass |
| FR-005 | Integration | Staff dashboard returns filtered results | Pass |
| FR-006 | Unit | Cannot delete table with future reservations | Pass |
| FR-007 | Unit | Mark no-show after 30-minute window | Pass |
| NFR-003 | Integration | No double-booking under concurrent requests | Pass |

---

## Sample Test Code

### Unit Test: Create Reservation (FR-001)

```typescript
import { describe, it, expect, beforeEach, vi } from 'vitest';
import { ReservationService } from '../src/services/reservation';
import { AvailabilityService } from '../src/services/availability';

describe('ReservationService.create', () => {
  let reservationService: ReservationService;
  let availabilityService: AvailabilityService;

  beforeEach(() => {
    availabilityService = new AvailabilityService(mockPrisma);
    reservationService = new ReservationService(mockPrisma, availabilityService);
  });

  it('creates a reservation when a table is available', async () => {
    // Arrange
    vi.spyOn(availabilityService, 'findAvailableTable').mockResolvedValue({
      id: 'table-1',
      number: 5,
      minCapacity: 2,
      maxCapacity: 6,
      location: 'indoor',
      isActive: true,
    });

    const input = {
      customerName: 'Jane Doe',
      customerPhone: '+1-555-0100',
      partySize: 4,
      reservationTime: new Date('2026-03-15T19:00:00Z'),
    };

    // Act
    const result = await reservationService.create(input);

    // Assert
    expect(result.confirmationCode).toMatch(/^[A-Z0-9]{8}$/);
    expect(result.tableId).toBe('table-1');
    expect(result.status).toBe('confirmed');
    expect(result.partySize).toBe(4);
  });

  it('rejects reservation when no table is available', async () => {
    // Arrange — Spec: FR-001 AC3
    vi.spyOn(availabilityService, 'findAvailableTable').mockResolvedValue(null);

    const input = {
      customerName: 'Jane Doe',
      customerPhone: '+1-555-0100',
      partySize: 20,
      reservationTime: new Date('2026-03-15T19:00:00Z'),
    };

    // Act & Assert
    await expect(reservationService.create(input))
      .rejects.toThrow('NO_AVAILABILITY');
  });
});
```

### Unit Test: Modification Time Window (FR-003)

```typescript
describe('ReservationService.modify', () => {
  it('rejects modification less than 2 hours before reservation', async () => {
    // Arrange — Spec: FR-003 AC1
    const reservation = createMockReservation({
      reservationTime: new Date('2026-03-15T19:00:00Z'),
      status: 'confirmed',
    });

    vi.spyOn(reservationService, 'findByConfirmationCode')
      .mockResolvedValue(reservation);

    // Simulate current time being 1 hour before reservation
    vi.setSystemTime(new Date('2026-03-15T18:00:00Z'));

    // Act & Assert
    await expect(
      reservationService.modify('ABC12345', { partySize: 6 })
    ).rejects.toThrow('MODIFICATION_WINDOW_CLOSED');
  });

  it('allows modification more than 2 hours before reservation', async () => {
    // Arrange
    const reservation = createMockReservation({
      reservationTime: new Date('2026-03-15T19:00:00Z'),
      status: 'confirmed',
    });

    vi.spyOn(reservationService, 'findByConfirmationCode')
      .mockResolvedValue(reservation);
    vi.spyOn(availabilityService, 'findAvailableTable')
      .mockResolvedValue(mockTable);

    // Simulate current time being 4 hours before reservation
    vi.setSystemTime(new Date('2026-03-15T15:00:00Z'));

    // Act
    const result = await reservationService.modify('ABC12345', { partySize: 6 });

    // Assert
    expect(result.partySize).toBe(6);
    expect(result.confirmationCode).toBe('ABC12345'); // FR-003 AC3: code unchanged
  });
});
```

### Unit Test: Idempotent Cancellation (FR-004)

```typescript
describe('ReservationService.cancel', () => {
  it('returns success when cancelling an already cancelled reservation', async () => {
    // Arrange — Spec: FR-004 AC3 (idempotent)
    const reservation = createMockReservation({
      status: 'cancelled',
      reservationTime: new Date('2026-03-15T19:00:00Z'),
    });

    vi.spyOn(reservationService, 'findByConfirmationCode')
      .mockResolvedValue(reservation);

    vi.setSystemTime(new Date('2026-03-15T12:00:00Z'));

    // Act
    const result = await reservationService.cancel('ABC12345');

    // Assert
    expect(result.status).toBe('cancelled');
    // No error thrown — idempotent
  });
});
```

### Integration Test: No Double-Booking (NFR-003)

```typescript
describe('Double-booking prevention', () => {
  it('prevents two reservations from booking the same table at the same time', async () => {
    // Arrange — Spec: NFR-003
    const tableId = await createTestTable({ minCapacity: 2, maxCapacity: 4 });
    const reservationTime = new Date('2026-03-15T19:00:00Z');

    // Act — attempt two concurrent reservations
    const [result1, result2] = await Promise.allSettled([
      reservationService.create({
        customerName: 'Alice',
        customerPhone: '+1-555-0001',
        partySize: 2,
        reservationTime,
      }),
      reservationService.create({
        customerName: 'Bob',
        customerPhone: '+1-555-0002',
        partySize: 2,
        reservationTime,
      }),
    ]);

    // Assert — one should succeed, one should fail (or get a different table)
    const successes = [result1, result2].filter(r => r.status === 'fulfilled');
    const failures = [result1, result2].filter(r => r.status === 'rejected');

    // If only one table exists, exactly one booking should succeed
    expect(successes.length).toBe(1);
    expect(failures.length).toBe(1);
  });
});
```

### Unit Test: No-Show Detection (FR-007)

```typescript
describe('No-show handling', () => {
  it('marks reservation as no-show after 30-minute window', async () => {
    // Arrange — Spec: FR-007 AC1
    const reservation = createMockReservation({
      reservationTime: new Date('2026-03-15T19:00:00Z'),
      status: 'confirmed',
    });

    // Simulate 31 minutes after reservation time
    vi.setSystemTime(new Date('2026-03-15T19:31:00Z'));

    // Act
    await noShowService.detectAndMark();

    // Assert
    const updated = await reservationService.findByConfirmationCode(
      reservation.confirmationCode
    );
    expect(updated.status).toBe('no_show');
  });

  it('does not mark reservation as no-show within 30-minute window', async () => {
    // Arrange
    const reservation = createMockReservation({
      reservationTime: new Date('2026-03-15T19:00:00Z'),
      status: 'confirmed',
    });

    // Simulate 25 minutes after reservation time
    vi.setSystemTime(new Date('2026-03-15T19:25:00Z'));

    // Act
    await noShowService.detectAndMark();

    // Assert
    const updated = await reservationService.findByConfirmationCode(
      reservation.confirmationCode
    );
    expect(updated.status).toBe('confirmed');
  });
});
```

---

## Verification Summary

| Category | Total | Pass | Fail | Coverage |
|----------|-------|------|------|----------|
| FR-001: Create | 4 | 4 | 0 | Full |
| FR-002: View | 2 | 2 | 0 | Full |
| FR-003: Modify | 3 | 3 | 0 | Full |
| FR-004: Cancel | 3 | 3 | 0 | Full |
| FR-005: Dashboard | 1 | 1 | 0 | Full |
| FR-006: Tables | 1 | 1 | 0 | Partial |
| FR-007: No-show | 1 | 1 | 0 | Full |
| NFR-003: Integrity | 1 | 1 | 0 | Full |
| **Total** | **16** | **16** | **0** | — |

All spec-mapped tests passing. Ready to proceed to Commit phase.
