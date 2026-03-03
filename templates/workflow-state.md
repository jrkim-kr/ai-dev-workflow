# Workflow State: [Project Name]

> This file tracks the current state of the development workflow.
> It is managed by the `dev-workflow` skill and should not be edited manually.

---

## Current State

```yaml
project: "[Project Name]"
current_phase: "plan"
started_at: "[YYYY-MM-DDTHH:MM:SSZ]"
last_updated: "[YYYY-MM-DDTHH:MM:SSZ]"
```

---

## Phase Status

| Phase | Status | Started | Completed | Approved By |
|-------|--------|---------|-----------|-------------|
| Plan | pending | — | — | — |
| Design | pending | — | — | — |
| Implement | pending | — | — | — |
| Review | pending | — | — | — |
| Test | pending | — | — | — |
| Commit | pending | — | — | — |

**Status values:** `pending` | `in_progress` | `completed` | `blocked`

---

## Transition Log

| Timestamp | From Phase | To Phase | Trigger | Notes |
|-----------|-----------|----------|---------|-------|
| [ISO timestamp] | — | Plan | Workflow initialized | |

---

## Artifacts

| Artifact | Phase | Path | Status |
|----------|-------|------|--------|
| spec.md | Plan | docs/spec/spec.md | pending |
| system-design.md | Plan | docs/architecture/system-design.md | pending |
| migrations/ | Plan | docs/schema/migrations/ | pending |
| plan.md | Design | docs/plan/plan.md | pending |
| checklist.md | Design | docs/plan/checklist.md | pending |
| Implementation | Implement | src/ | pending |
| Review Report | Review | — | pending |
| Test Suite | Test | tests/ | pending |
| Verification Report | Test | docs/test/verification-report.md | pending |

---

## Flags

```yaml
spec_approved: false
plan_approved: false
impl_complete: false
review_passed: false
tests_passed: false
committed: false
```

---

## Blockers

| # | Phase | Description | Status | Resolution |
|---|-------|-------------|--------|------------|
| — | — | No blockers | — | — |

---

## Notes

[Any additional context about the current workflow state]
