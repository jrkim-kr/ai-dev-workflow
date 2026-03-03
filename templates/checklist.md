# Verification Checklist: [Project Name]

> **Date:** [YYYY-MM-DD]
> **Spec Reference:** spec.md v[version]
> **Plan Reference:** plan.md v[version]

---

## Spec Compliance

Verify each functional requirement is implemented as specified.

| Requirement | Description | Implemented | Tested | Notes |
|-------------|-------------|:-----------:|:------:|-------|
| FR-001 | [Description] | [ ] | [ ] | |
| FR-002 | [Description] | [ ] | [ ] | |
| FR-003 | [Description] | [ ] | [ ] | |

---

## Non-Functional Requirements

| Requirement | Target | Measured | Pass | Notes |
|-------------|--------|----------|:----:|-------|
| NFR-001 | [Target metric] | [Actual metric] | [ ] | |
| NFR-002 | [Target metric] | [Actual metric] | [ ] | |

---

## Code Quality

- [ ] No duplicated logic (DRY)
- [ ] Functions/methods have single responsibility
- [ ] Naming is clear and consistent
- [ ] No hardcoded values that should be configurable
- [ ] Error messages are informative
- [ ] No unused code, imports, or variables

---

## Security

- [ ] All user input is validated at system boundaries
- [ ] SQL queries use parameterized statements
- [ ] Authentication is required on all protected endpoints
- [ ] Sensitive data is not logged
- [ ] No hardcoded secrets or credentials
- [ ] Dependencies have no known critical vulnerabilities
- [ ] CORS is configured correctly

---

## Error Handling

- [ ] All external API calls have error handling
- [ ] Database operations handle connection failures
- [ ] File operations handle missing/permission errors
- [ ] User-facing errors don't expose internal details
- [ ] All error paths are tested

---

## Testing

- [ ] Unit tests cover all core business logic
- [ ] Integration tests cover component interactions
- [ ] E2E tests cover critical user paths
- [ ] Edge cases from spec are tested
- [ ] All tests passing
- [ ] No skipped or disabled tests without documented reason

---

## Documentation

- [ ] API endpoints are documented
- [ ] Environment variables are documented
- [ ] Setup instructions are accurate and complete
- [ ] Architecture decisions are recorded

---

## Deployment Readiness

- [ ] Environment configuration is externalized
- [ ] Database migrations run cleanly
- [ ] Health check endpoint exists
- [ ] Logging is structured and at appropriate levels
- [ ] No debug code in production paths

---

## Summary

| Category | Items | Passed | Failed | Skipped |
|----------|-------|--------|--------|---------|
| Spec Compliance | | | | |
| Non-Functional | | | | |
| Code Quality | | | | |
| Security | | | | |
| Error Handling | | | | |
| Testing | | | | |
| Documentation | | | | |
| Deployment | | | | |
| **Total** | | | | |

**Overall Status:** Pass / Fail / Conditional Pass

**Reviewer:** [Name]
**Date:** [YYYY-MM-DD]
