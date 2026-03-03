# code-review

**Structured code review skill with spec compliance verification.**

Performs systematic code review that checks implementation against the original specification, identifies quality issues, security risks, and architectural deviations.

---

## Purpose

AI-generated code passes syntax checks. It compiles. It might even run. But does it satisfy the specification? Does it follow the project's architectural patterns? Does it handle edge cases? This skill applies structured review criteria to answer those questions before code reaches production.

---

## Trigger

Activated during:
- Phase 4 (Review) of the dev-workflow
- Manual invocation for targeted code review
- Pull request review context

---

## Review Dimensions

### 1. Spec Compliance

For each functional requirement in `spec.md`:
- Is the requirement implemented?
- Does the implementation match the acceptance criteria?
- Are there deviations from the specified behavior?

**Output:** Compliance matrix with pass/fail/partial status per requirement.

### 2. Code Quality

Check for violations of established engineering principles:

- **Single Responsibility:** Does each module/class/function do one thing?
- **Open/Closed:** Can behavior be extended without modifying existing code?
- **Dependency Inversion:** Do modules depend on abstractions, not implementations?
- **DRY:** Is there duplicated logic that should be extracted?
- **Naming:** Are variables, functions, and files named clearly and consistently?

### 3. Security

Scan for common vulnerability patterns:

- **Injection:** SQL injection, command injection, XSS vectors
- **Authentication:** Hardcoded credentials, weak token handling, missing auth checks
- **Data Exposure:** Sensitive data in logs, unencrypted storage, overly broad API responses
- **Input Validation:** Missing validation at system boundaries
- **Dependency Risk:** Known vulnerabilities in dependencies

### 4. Error Handling

Verify error handling completeness:

- Are all external calls wrapped in error handling?
- Do errors propagate with useful context?
- Are failure modes defined in the spec properly handled?
- Is there graceful degradation where appropriate?

### 5. Architecture Consistency

Compare implementation against `plan.md`:

- Does the file structure match the planned architecture?
- Are component boundaries respected?
- Are API contracts implemented as designed?
- Are dependencies flowing in the correct direction?

---

## Severity Classification

| Level | Definition | Action Required |
|-------|-----------|-----------------|
| **Critical** | Security vulnerability, data loss risk, spec violation | Must fix before proceeding |
| **Major** | Quality issue, missing error handling, architectural deviation | Must fix before proceeding |
| **Minor** | Style inconsistency, naming improvement, optional optimization | Fix recommended, not blocking |
| **Info** | Observation, suggestion, future consideration | No action required |

---

## Review Output Format

```markdown
# Code Review Report

**Project:** <name>
**Date:** <ISO date>
**Reviewer:** AI Code Review
**Phase:** Review (Phase 4)

## Summary
- Critical findings: <N>
- Major findings: <N>
- Minor findings: <N>
- Spec compliance: <N>/<total> requirements verified

## Findings

### [CRITICAL] SQL Injection in user search endpoint
**File:** src/routes/users.ts:42
**Issue:** User input passed directly to SQL query without parameterization
**Fix:** Use parameterized query: `db.query('SELECT * FROM users WHERE name = ?', [input])`

### [MAJOR] Missing error handling for payment API
**File:** src/services/payment.ts:87
**Issue:** External API call has no try/catch, timeout, or retry logic
**Fix:** Wrap in try/catch, add 30s timeout, implement retry with exponential backoff

## Spec Compliance Matrix
| Requirement | Status | Notes |
|-------------|--------|-------|
| FR-001 | Pass | Fully implemented as specified |
| FR-002 | Partial | Missing edge case for empty input |
| FR-003 | Fail | Not implemented |

## Recommendation
<proceed / fix and re-review / return to implementation>
```

---

## Output Artifacts

- Code review report
- Annotated findings with file/line references
- Spec compliance matrix
- Updated `workflow-state.md`

---

## Acknowledgments

The review dimensions and severity classification in this skill were inspired by [sanyuan0704/sanyuan-skills](https://github.com/sanyuan0704/sanyuan-skills), specifically the `code-review-expert` skill. Its approach to structured code review covering SOLID principles, security analysis, and actionable improvement proposals served as a foundation for this skill's design.
