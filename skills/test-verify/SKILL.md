---
name: test-verify
description: "Analyze the project's spec.md and code to generate test cases, run automated/manual tests, and produce a verification report. Supports unit, integration, and E2E testing."
argument-hint: "[unit | integration | e2e | all | verify (optional)]"
user-invokable: true
---

# Test & Verification Skill

## Purpose

Analyze the project's feature specification (spec.md) and source code to generate test cases, run tests, and produce an implementation verification report.

## Execution Steps

### Phase 1: Project Analysis & Environment Setup

1. **Detect and auto-install test environment:**
   - Read `package.json` → check test framework
   - Supported frameworks: Vitest, Jest, Playwright, Cypress, Testing Library
   - Check test config files (`vitest.config.*`, `jest.config.*`, `playwright.config.*`)
   - Scan for existing test files (`**/*.test.*`, `**/*.spec.*`, `**/__tests__/**`)

   **Auto-install procedure if framework is missing:**
   - Ask user via AskUserQuestion:
     - header: "Test Framework"
     - question: "No test framework is installed. Which framework would you like to install?"
     - options:
       - "Vitest + Testing Library (Recommended)" — Optimal for Vite-based projects
       - "Jest + Testing Library" — Traditional choice
       - "Manual verification only (no install)" — Generate manual verification guide only, no automated tests
   - After user confirmation:
     1. Install packages (`npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom`)
     2. Create config file (`vitest.config.ts` — adapted to project build tool)
     3. Add test scripts to `package.json` (`"test": "vitest run"`, `"test:watch": "vitest"`)
     4. Verify installation (`npm test -- --version` or equivalent)

2. **Analyze spec documents:**
   - Read `docs/spec/spec.md` → extract feature list, state flows, per-page behaviors
   - Read `docs/plan/plan.md` → identify implemented steps
   - Read `docs/plan/checklist.md` → identify verification items (**assertion mapping source**)

3. **checklist.md → assertion mapping analysis:**
   - Parse the "verification method" column from each checklist item
   - Classify verification items as automatable vs. manual-only:

   | Automatable | Requires Manual Verification |
   |------------|------------------------------|
   | Pure function I/O verification | Visual UI confirmation |
   | File existence check | DevTools-based performance measurement |
   | Data transformation correctness | Browser interaction |
   | Schema validation pass/fail | Memory usage monitoring |

   - Automatable items → convert to unit/integration test assertions
   - Manual verification items → include in E2E manual verification guide

4. **Analyze source code:**
   - Extract list of key functions/components
   - Identify external dependencies (API, DB, Web Worker) → determine mock targets
   - Check error handling paths

5. **Analyze and prepare test fixtures:**
   - Identify input data needed for tests (based on spec.md + checklist.md)
   - Create `__fixtures__/` or `__testdata__/` directory
   - Prepare by fixture type:

   | Fixture Type | Generation Method | Example |
   |-------------|-------------------|---------|
   | JSON config files | Copy + modify project built-in files | Valid/invalid config variants |
   | Minimal data samples | Generate ~10 rows based on actual format | CSV with header, without header, with BOM, etc. |
   | Database samples | Create in-memory or use test DB | Minimal schema with seed data |
   | Encoding test files | Various encodings | UTF-8, UTF-16, other project-relevant encodings |
   | Binary data | Hardcoded typed arrays | Edge case conversions |

   - Reuse existing fixtures if available; generate minimal samples via code if not
   - **Principle:** Fixtures should be minimal — include only rows/columns needed for the test purpose

6. **Confirm test scope with user:** (when no argument is provided)

Use AskUserQuestion:
- header: "Test Scope"
- question: "What scope of tests should be generated?"
- options:
  - "Full (unit + integration + e2e)" — Generate all test types
  - "Unit tests only" — Per-function/component unit tests
  - "Integration tests only" — API/data flow integration tests
  - "E2E scenarios only" — User scenario-based E2E guide

---

### Phase 2: Test Case Generation

#### Test Priority Strategy

When generating tests, **start with critical paths**:

```
Priority 1: Core business logic (critical path functions)
Priority 2: Data correctness (transformation/calculation results match expected values)
Priority 3: Error handling (invalid input, failure paths)
Priority 4: UI interactions (rendering, events)
Priority 5: Performance (benchmarks)
```

#### Unit Tests

For each function/component:

```
[Function/Component Name]
├── Happy Path
│   ├── Returns expected output for standard input
│   └── Various valid input combinations
├── Error Path
│   ├── Appropriate error for invalid input
│   └── Handling when external dependency fails
└── Edge Cases
    ├── Empty values (null, undefined, "", [], {})
    ├── Min/max values
    └── Special characters, Unicode
```

**Assertion specificity rules:**

Assertions must use **concrete expected values from spec.md/checklist.md**. Vague expressions like "returns correct result" are prohibited.

```typescript
// ❌ BAD — vague assertion
it('data processing works correctly', () => {
  const result = processData(input)
  expect(result).toBeTruthy()
})

// ✅ GOOD — concrete assertion based on spec/checklist
it('parseCSVRow splits comma-separated values correctly (checklist #12)', () => {
  const result = parseCSVRow('Alice,30,Engineer')
  expect(result).toEqual(['Alice', '30', 'Engineer'])
})

// ✅ GOOD — numeric correctness with decimal precision
it('calculateDiscount: 1000 * 0.15 = 150.0 (checklist #25)', () => {
  expect(calculateDiscount(1000, 0.15)).toBeCloseTo(150.0, 2)
})

// ✅ GOOD — checklist item number reference
it('formatCurrency: 1234567 → "$1,234,567" (checklist #33)', () => {
  expect(formatCurrency(1234567)).toBe('$1,234,567')
})
```

**Checklist item reference rules:**
- Include the related checklist item number in each test's `it()` description as `(checklist #N)`
- This enables tracking which checklist items pass/fail from test execution results

**Test file generation rules:**
- Create in the same directory as the source file or under `__tests__/`
- Filename: `[source-filename].test.[ts|tsx]` or `[source-filename].spec.[ts|tsx]`
- Follow existing test patterns if the project already has tests
- Use describe/it structure

**Test code template (Vitest/Jest):**

```typescript
import { describe, it, expect, vi } from 'vitest'  // or jest
import { targetFunction } from './target'

describe('targetFunction', () => {
  // Happy path
  describe('normal operation', () => {
    it('returns correct result for standard input (checklist #N)', () => {
      const result = targetFunction(validInput)
      expect(result).toEqual(expectedOutput)  // use concrete expected values
    })
  })

  // Error path
  describe('error handling', () => {
    it('throws error for invalid input (checklist #N)', () => {
      expect(() => targetFunction(invalidInput)).toThrow(/expected error message/)
    })
  })

  // Edge cases
  describe('boundary conditions', () => {
    it('returns empty result for empty array input', () => {
      expect(targetFunction([])).toEqual([])
    })
  })
})
```

**React component tests (Testing Library):**

```typescript
import { render, screen, fireEvent } from '@testing-library/react'
import { ComponentName } from './ComponentName'

describe('ComponentName', () => {
  it('displays correct UI on initial render', () => {
    render(<ComponentName />)
    expect(screen.getByText('expected text')).toBeInTheDocument()
  })

  it('state changes on button click', async () => {
    render(<ComponentName />)
    fireEvent.click(screen.getByRole('button', { name: 'button label' }))
    expect(screen.getByText('changed text')).toBeInTheDocument()
  })
})
```

#### Web Worker Test Strategy

Web Worker-based code is difficult to run directly in standard Node.js test environments. Apply the following strategy:

**1. Isolate and test worker internal logic (recommended):**
Separate business logic inside workers into pure functions and test them directly.

```typescript
// ✅ Separate worker internal logic into pure functions → import and test directly
import { processData, validateInput } from '@/lib/workers/data-processor'

describe('data-processor (worker internal logic)', () => {
  it('processes valid input and returns expected result (checklist #58)', async () => {
    const input = fixtures.sampleData
    const result = await processData(input, config)
    expect(result.items).toHaveLength(10)
  })
})
```

**2. Test worker message communication (integration):**
Test the Worker Manager's RPC call/response matching logic with a mock Worker.

```typescript
// Mock the Worker itself to verify message communication patterns
vi.mock('./workers/data-processor.worker', () => ({
  default: class MockWorker {
    onmessage: ((e: MessageEvent) => void) | null = null
    postMessage(data: unknown) {
      // mock response
      setTimeout(() => this.onmessage?.({ data: mockResponse } as MessageEvent), 0)
    }
    terminate() {}
  }
}))
```

**3. Delegate tests requiring actual Worker environment to E2E:**
Items requiring the actual Worker environment (Transferable transfers, Worker Pool concurrency, etc.) should be included in the manual verification guide.

#### Performance Tests (Benchmark)

Verify performance criteria defined in the checklist with automated benchmark tests.

**Using Vitest bench:**

```typescript
import { bench, describe } from 'vitest'

describe('performance benchmarks', () => {
  // checklist #42: search response < 200ms (10K records)
  bench('search 10K records (checklist #42)', () => {
    searchRecords(largeDataset10K, 'query')
  }, { time: 5000 })  // measure over 5 seconds

  // checklist #55: data processing < 500ms
  bench('process 100K rows of data', () => {
    processLargeDataset(dataset100K)
  }, { time: 5000 })
})
```

**Performance assertion rules:**
- When verifying checklist performance criteria (`< 10 seconds`, `< 100ms`) with `expect`, use the **median of 3 measurements**
- Allow **±50% margin** considering differences between CI and local environments
- Separate performance test files: `*.bench.ts`
- Measure pure computation performance only (exclude I/O, network)

```typescript
// Performance assertion example
it('batch import 1000 records < 5 seconds (checklist #60)', async () => {
  const measurements = []
  for (let i = 0; i < 3; i++) {
    const start = performance.now()
    await batchImport(thousandRecords)
    measurements.push(performance.now() - start)
  }
  const median = measurements.sort((a, b) => a - b)[1]
  expect(median).toBeLessThan(5_000)  // 5 seconds = 5,000ms
})
```

#### Integration Tests

API/data flow tests:
- Request/response verification per API endpoint
- State transition flow verification (data flow between state stores)
- Use mocks for external services
- **Data pipeline E2E:** input → processing → output result verification (using Worker mock if applicable)

```typescript
// Integration test example: input → process → output
describe('data pipeline integration', () => {
  it('CSV upload → parse → store in DB (checklist #45)', async () => {
    const csvFile = fixtures.sampleCSV
    const config = fixtures.importConfig

    const parsed = await parseCSV(csvFile, config)
    const result = await storeRecords(parsed.rows)

    // All rows imported
    expect(result.imported).toBe(parsed.rows.length)
    expect(result.errors).toHaveLength(0)

    // Data integrity verified
    const stored = await fetchRecords({ limit: 1 })
    expect(stored[0].name).toBe(parsed.rows[0].name)
  })
})
```

#### E2E Tests / Manual Verification Guide

If automated E2E is configured, generate Playwright/Cypress test files.
If not configured, generate a **manual verification guide**:

```markdown
## Manual Verification Guide

### Scenario 1: [Scenario Name] (Core Flow)

**Preconditions:**
- [Condition 1]
- [Condition 2]

**Verification Steps:**
| Order | Action | Expected Result | Checklist # | Confirmed |
|-------|--------|-----------------|-------------|-----------|
| 1 | [Specific action] | [Expected result] | #N | [ ] |
| 2 | [Specific action] | [Expected result] | #N | [ ] |
| 3 | [Specific action] | [Expected result] | #N | [ ] |

**Error Scenarios:**
| Order | Action | Expected Result | Checklist # | Confirmed |
|-------|--------|-----------------|-------------|-----------|
| 1 | [Abnormal input] | [Error message displayed] | #N | [ ] |
```

Include checklist item numbers in the manual verification guide for traceability.

---

### Phase 3: Test Execution

1. **Run automated tests:**
   - Execute with the project's test command (e.g., `npm test`, `npx vitest run`)
   - Capture execution results

2. **Analyze results:**
   - Classify pass/fail items
   - Analyze failure causes
   - Report coverage (if configured)

---

### Phase 4: Verification Report

#### When argument is `verify`

Run existing tests and produce a comprehensive verification report based on checklist.md.

```markdown
## Verification Report

> Generated: [Date]

### 1. Test Execution Results

| Type | Total | Pass | Fail | Skip |
|------|-------|------|------|------|
| Unit Tests | N | N | N | N |
| Integration Tests | N | N | N | N |
| E2E | N | N | N | N |

### 2. Coverage (if available)

| Metric | Value |
|--------|-------|
| Statements | N% |
| Branches | N% |
| Functions | N% |
| Lines | N% |

### 3. Failed Items Detail

#### Failure #1: [Test Name]
- **File:** `path/to/test.ts:line`
- **Error:** [Error message]
- **Root Cause Analysis:** [Cause]
- **Suggested Fix:** [Suggestion]

### 4. checklist.md Progress

- Total items: N
- Completed: N (N%)
- Incomplete: N

### 5. spec.md Compliance Check

| Feature | Test Exists | Test Passes | Notes |
|---------|------------|-------------|-------|
| ... | ✅ / ❌ | ✅ / ❌ | ... |

### 6. Overall Verdict

**Verdict: PASS / FAIL / CONDITIONAL_PASS**

- **PASS**: All tests pass, 100% of checklist automatable items completed
- **CONDITIONAL_PASS**: ALL of the following must be met:
  - 0 P0/P1 test failures
  - P2 or lower failures ≤ 10% of total tests (or 5 or fewer)
  - All core data pipeline (file→decode→output) related tests pass
  - All core flow scenarios in manual verification guide pass
- **FAIL**: Any condition above not met, or P0/P1 issues exist

### 7. Next Steps
- [Specific next action guidance]
```

---

## Writing Rules

1. **Respect existing test patterns**: If the project already has tests, follow their patterns/structure.
2. **Practical tests**: Test behavior, not implementation details.
3. **Minimize mocking**: Use real implementations over mocks when possible.
4. **Test independence**: Each test must be independently executable.
5. **Auto-detect framework**: Detect test framework from package.json and generate appropriate code.
