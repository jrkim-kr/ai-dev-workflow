# test-verify — Test & Verification

Analyzes project spec and code to generate test cases, run tests, and produce a verification report. Supports unit, integration, and E2E tests.

## Arguments

| Argument | Action |
|----------|--------|
| (none) | Ask user for scope, then generate |
| `unit` | Unit tests only |
| `integration` | Integration tests only |
| `e2e` | E2E / manual verification guide |
| `all` | Generate all test types |
| `verify` | Run existing tests + produce verification report against checklist |

## Procedure

### Phase 1: Analysis & Environment Setup

1. **Detect test framework**: Read package.json → identify Vitest/Jest/Playwright/Cypress
   - If none found: ask user which to install (Vitest recommended)
   - Auto-install, create config, add test scripts

2. **Parse spec documents**:
   - `docs/spec/spec.md` → feature list, state flows, per-page behavior
   - `docs/plan/plan.md` → implemented steps
   - `docs/plan/checklist.md` → verification items (**assertion mapping source**)

3. **Checklist → assertion mapping**: Classify each item as automatable vs. manual verification

4. **Source code analysis**: Extract functions/components, identify mock targets, error handling paths

5. **Prepare test fixtures**: Create `__fixtures__/` or `__testdata__/` with minimal samples

### Phase 2: Test Case Generation

**Priority order:**
1. Core data pipeline (input → processing → output)
2. Data integrity (transformation correctness)
3. Error handling (invalid input, failure paths)
4. UI interaction (rendering, events)
5. Performance (benchmarks)

**Assertion rules:**
- Must use **concrete expected values from spec/checklist** — no vague assertions
- Reference checklist item number in `it()` descriptions: `(checklist #N)`
- Numeric precision: specify decimal places explicitly

**Unit tests**: Happy path → error cases → edge cases (null, empty, min/max, unicode) per function/component

**Integration tests**: API endpoint request/response, state transitions, data pipeline end-to-end (with mocks for external deps)

**E2E tests / Manual verification guide**: If Playwright/Cypress configured → generate test files. Otherwise → generate manual verification guide with step-by-step scenarios.

**Performance tests** (if checklist specifies performance targets):
- Use `vitest bench` or 3-measurement median approach
- Allow ±50% margin for CI/local environment differences
- Separate files: `*.bench.ts`

### Phase 3: Test Execution

Run project test command (e.g., `npm test`, `npx vitest run`). Capture results.

### Phase 4: Verification Report

```markdown
## Verification Report

### 1. Test Results
| Type | Total | Pass | Fail | Skip |
|------|-------|------|------|------|

### 2. Coverage (if available)
| Metric | Value |

### 3. Failure Details
#### Failure #1: [test name]
- File, error, root cause analysis, fix suggestion

### 4. Checklist Progress
- Total items: N, Checked: N (N%)

### 5. Spec Compliance
| Feature | Tests Exist | Tests Pass | Notes |

### 6. Verdict: PASS / FAIL / CONDITIONAL_PASS
```

**Verdict criteria:**
- **PASS**: All tests pass, checklist automation items 100% complete
- **CONDITIONAL_PASS**: No P0/P1 failures, P2 failures ≤ 10% (or ≤ 5), core pipeline tests pass
- **FAIL**: Any of the above not met, or P0/P1 issues exist

## Output Artifacts

- Test files: `*.test.ts`, `*.bench.ts`
- Test fixtures: `__fixtures__/` or `__testdata__/`
- Manual verification guide: `docs/test/manual-verification.md`
- Verification report: `docs/test/verification-report.md`

## Rules

1. Respect existing test patterns — match project conventions
2. Test behavior, not implementation details
3. Minimize mocks — prefer real implementations when possible
4. Each test must be independently runnable
5. Auto-detect test framework from package.json
