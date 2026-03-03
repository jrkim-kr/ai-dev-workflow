# Specification Review Process

This document defines how specifications are reviewed and approved within the `ai-dev-workflow`. The spec review is the most critical quality gate in the entire workflow — every downstream artifact depends on it.

---

## Why Spec Review Matters

A specification error has the highest cost multiplier of any defect:

```
Spec error found during:
  Planning    → cost: 1x   (fix the document)
  Design      → cost: 5x   (redesign affected components)
  Implementation → cost: 10x  (rewrite code)
  Testing     → cost: 20x  (rewrite code + tests)
  Production  → cost: 100x (incident + fix + redeploy + trust damage)
```

Investing time in spec review is the highest-ROI activity in any development workflow.

---

## Review Dimensions

### 1. Completeness

Every requirement area must be addressed. Missing requirements are the most dangerous type of defect because they're invisible until implementation.

**Checklist:**
- [ ] All user-facing functionality is specified
- [ ] Error scenarios are defined for each feature
- [ ] Edge cases are identified and documented
- [ ] Data model covers all referenced entities
- [ ] API contracts exist for all system boundaries
- [ ] Non-functional requirements have measurable targets
- [ ] Out of scope section explicitly excludes common assumptions

### 2. Testability

Every requirement must be verifiable. If you can't write a test for it, you can't prove it works.

**Checklist:**
- [ ] Every functional requirement has at least one acceptance criterion
- [ ] Acceptance criteria are measurable (not subjective)
- [ ] Performance targets include specific metrics and conditions
- [ ] Security requirements specify what must be true, not vague goals

**Anti-patterns:**
| Untestable | Testable |
|-----------|----------|
| "The system should be fast" | "API response time < 200ms at p95 under 1000 concurrent users" |
| "Users can manage data easily" | "Users can CRUD their profile; each operation completes in < 1 second" |
| "The system should be secure" | "All endpoints require JWT; passwords use bcrypt with cost 12" |

### 3. Consistency

No requirement should contradict another. As specs grow, contradictions creep in through incremental additions.

**Checklist:**
- [ ] No two requirements specify conflicting behavior for the same input
- [ ] Data model constraints align with business rules
- [ ] API response formats are consistent across endpoints
- [ ] Terminology is used consistently (no synonyms for the same concept)
- [ ] Priority assignments don't create impossible dependency chains

### 4. Feasibility

Every requirement must be implementable within the project's constraints.

**Checklist:**
- [ ] Technology stack can support all requirements
- [ ] Performance targets are achievable with the chosen architecture
- [ ] Third-party dependencies exist and are stable
- [ ] Scope is realistic for the project timeline
- [ ] No requirements assume capabilities that don't exist

### 5. Traceability

Every requirement must have a unique identifier that can be referenced throughout the workflow.

**Checklist:**
- [ ] All functional requirements have IDs (FR-001, FR-002, ...)
- [ ] All non-functional requirements have IDs (NFR-001, NFR-002, ...)
- [ ] Data model entities are named consistently with requirements
- [ ] API endpoints reference the requirements they implement

---

## Review Process

### Step 1: Self-Review (Author)

Before presenting the spec for external review, the author verifies:

1. All sections of the spec template are completed
2. No placeholder text remains
3. Quality criteria above are satisfied
4. Open questions are clearly marked

### Step 2: Structured Review

The reviewer (user/stakeholder) evaluates the spec along each dimension:

```
For each requirement:
  1. Is it complete? (all necessary detail is present)
  2. Is it testable? (acceptance criteria exist and are measurable)
  3. Is it consistent? (no conflicts with other requirements)
  4. Is it feasible? (can be implemented as specified)
  5. Is it traceable? (has a unique ID, can be referenced)
```

### Step 3: Feedback Classification

Review findings are classified by severity:

| Severity | Definition | Action |
|----------|-----------|--------|
| **Blocking** | Spec cannot be approved as-is | Must be resolved before approval |
| **Major** | Significant gap or ambiguity | Should be resolved before approval |
| **Minor** | Improvement opportunity | Can be addressed in future revision |
| **Question** | Clarification needed | Answer required, may or may not block |

### Step 4: Resolution

For each finding:
1. Author addresses the feedback
2. Spec is updated
3. Reviewer verifies the resolution
4. Finding is marked as resolved

### Step 5: Approval

The spec is approved when:
- All blocking and major findings are resolved
- Minor findings are documented for future reference
- All questions are answered
- Reviewer explicitly states approval

---

## Common Spec Failures

### The "Happy Path Only" Spec

**Problem:** Only specifies what happens when everything goes right.
**Fix:** For every feature, add: "What happens when [input is invalid / service is down / user cancels / data doesn't exist]?"

### The "Implementation Spec"

**Problem:** Describes how to build it instead of what it should do.
**Fix:** Remove implementation details. Focus on behavior and outcomes. The design phase handles the "how."

### The "Everything Is Must-Have" Spec

**Problem:** No prioritization. Everything is critical.
**Fix:** Force-rank requirements. If everything is must-have, nothing is. Use MoSCoW (Must/Should/Could/Won't) or similar.

### The "Assumed Context" Spec

**Problem:** Relies on knowledge that isn't written down.
**Fix:** Read the spec as if you have no prior context. If anything is unclear, it needs more detail.

### The "Moving Target" Spec

**Problem:** Requirements change during implementation without formal revision.
**Fix:** Spec changes trigger a new workflow cycle. If the spec changes, design and implementation must be re-evaluated against the new spec.

---

## Spec Versioning

When a spec is revised:
1. Increment the version number
2. Document what changed and why
3. Assess impact on existing design/implementation
4. If the change affects approved artifacts, those artifacts must be re-reviewed

The spec version is recorded in `workflow-state.md` so that all downstream artifacts can be traced to the exact spec version they were built against.
