# test-verify

**Spec-mapped test generation and verification skill.**

Generates test cases derived directly from specification requirements, executes them, and produces a verification report mapping test results back to functional requirements.

---

## Purpose

Tests written without reference to specifications are incomplete by definition. They test what the developer thought of, not what the system requires. This skill closes that gap by treating the specification as the single source of truth for test generation.

---

## Trigger

Activated during:
- Phase 5 (Test) of the dev-workflow
- Manual invocation for targeted test generation
- Post-fix verification after review findings are addressed

---

## Actions

### 1. Parse Specification

Read `spec.md` and extract:
- Functional requirements (each becomes a test target)
- Acceptance criteria (each becomes a test assertion)
- Edge cases (each becomes a boundary test)
- Error scenarios (each becomes a negative test)
- Data constraints (each becomes a validation test)

### 2. Generate Test Matrix

Create a mapping table:

```markdown
| Spec Requirement | Test Type | Test Description | Status |
|-----------------|-----------|------------------|--------|
| FR-001: User login | Unit | Validate credentials against DB | Pending |
| FR-001: User login | Unit | Reject invalid password | Pending |
| FR-001: User login | Integration | Full login flow with session | Pending |
| FR-002: Rate limiting | Unit | Block after N attempts | Pending |
| FR-002: Rate limiting | E2E | Rate limit resets after timeout | Pending |
```

### 3. Generate Test Code

For each entry in the test matrix, generate executable test code:

- **Unit tests:** Test individual functions/methods in isolation
- **Integration tests:** Test component interactions and data flow
- **E2E tests:** Test complete user workflows end-to-end

Test code follows the project's existing test framework and conventions. If no test framework is established, recommend one based on the technology stack.

### 4. Execute Tests

Run the full test suite and capture:
- Pass/fail status for each test
- Error messages for failures
- Execution time
- Coverage metrics (if tooling is available)

### 5. Generate Verification Report

Produce `verification-report.md`:

```markdown
# Verification Report

**Project:** <project name>
**Date:** <ISO date>
**Spec Version:** <spec hash or date>

## Summary
- Total requirements: <N>
- Requirements with test coverage: <N>
- Requirements without coverage: <N>
- Tests passed: <N>/<total>
- Tests failed: <N>/<total>

## Coverage Matrix
| Requirement | Tests | Pass | Fail | Coverage |
|-------------|-------|------|------|----------|
| FR-001 | 3 | 3 | 0 | Full |
| FR-002 | 2 | 1 | 1 | Partial |

## Failures
### FR-002: Rate limiting — Block after N attempts
- **Expected:** Request rejected after 5 failed attempts
- **Actual:** Request accepted on 6th attempt
- **Root cause:** Off-by-one error in attempt counter
- **Severity:** Major

## Uncovered Requirements
- NFR-003: Response time < 200ms (requires performance testing tooling)

## Recommendation
<overall assessment and recommended next steps>
```

---

## Test Classification

| Type | Scope | Speed | When to Use |
|------|-------|-------|-------------|
| **Unit** | Single function/method | Fast (ms) | Every requirement with testable logic |
| **Integration** | Component interaction | Medium (s) | Data flow, API contracts, DB operations |
| **E2E** | Full user workflow | Slow (s-min) | Critical user paths, acceptance criteria |

### Coverage Priority

1. **Critical path first:** Test the primary success scenarios before edge cases
2. **Spec-mapped:** Every test must trace back to a spec requirement
3. **Failure modes:** Test what should fail, not just what should succeed
4. **Boundary conditions:** Test limits defined in the spec (max length, min value, etc.)

---

## Output Artifacts

- Test matrix mapping (in `verification-report.md`)
- Generated test files (in project's test directory)
- Verification report (`verification-report.md`)
- Updated `workflow-state.md` with test results

---

## Failure Handling

When tests fail:
1. **Classify the failure:** Is it a test bug or an implementation bug?
2. **If test bug:** Fix the test, re-run, document the test correction
3. **If implementation bug:** Document the finding, return to implementation phase to fix
4. **If spec ambiguity:** Flag the requirement, request clarification, update spec if needed

The skill does not mark the Test phase as complete until all spec-mapped tests pass.
