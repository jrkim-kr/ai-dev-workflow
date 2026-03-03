# Phase Lifecycle

This document describes the detailed lifecycle of each phase in the `ai-dev-workflow`, including entry conditions, actions, exit conditions, and failure modes.

---

## Lifecycle Overview

```
  ┌───────┐   approve   ┌────────┐   approve   ┌──────────┐
  │ PLAN  │ ──────────▶ │ DESIGN │ ──────────▶ │IMPLEMENT │
  │       │             │        │             │          │
  │spec.md│             │plan.md │             │  code    │
  └───────┘             └────────┘             └──────────┘
                                                    │
                                                    │ complete
                                                    ▼
  ┌────────┐   pass     ┌────────┐   pass      ┌────────┐
  │ COMMIT │ ◀───────── │  TEST  │ ◀────────── │ REVIEW │
  │        │            │        │             │        │
  │commits │            │ tests  │             │findings│
  └────────┘            └────────┘             └────────┘
```

---

## Phase 1: Plan

### Purpose
Transform raw requirements into a structured, testable specification.

### Entry Conditions
- None (initial phase)

### Process

```
1. Gather Input
   ├── User provides requirements (verbal, written, existing code)
   ├── Analyze existing codebase (if applicable)
   └── Identify constraints and assumptions

2. Draft Specification
   ├── Functional requirements (numbered, testable)
   ├── Non-functional requirements (measurable)
   ├── Data model (entities, relationships)
   ├── API contracts (endpoints, formats)
   ├── Acceptance criteria (per requirement)
   └── Out of scope (explicit exclusions)

3. Validate Specification
   ├── Every requirement has acceptance criteria
   ├── No vague language without metrics
   ├── No contradictions between requirements
   ├── Data model covers all referenced entities
   └── API contracts align with requirements

4. Present for Approval
   ├── Highlight key decisions
   ├── List assumptions
   ├── Flag open questions
   └── Request user review
```

### Exit Conditions
- `spec.md` exists and is complete
- User has explicitly approved the specification
- `workflow-state.md` updated: `spec_approved: true`

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Incomplete spec | Missing requirements | Re-elicit from user, update spec |
| Spec rejected | Requirements don't match intent | Revise based on feedback, re-present |
| Ambiguous requirements | Vague input from user | Ask specific questions, propose concrete alternatives |

---

## Phase 2: Design

### Purpose
Translate the approved specification into a concrete system design and implementation plan.

### Entry Conditions
- `spec_approved: true` in workflow state
- `spec.md` exists and is accessible

### Process

```
1. Architecture Design
   ├── Select technology stack (with rationale)
   ├── Define component boundaries
   ├── Design data flow between components
   └── Map spec requirements to components

2. Implementation Planning
   ├── Define file structure
   ├── Break down into ordered tasks
   ├── Identify task dependencies
   ├── Map each task to spec requirements
   └── Assess complexity per task

3. Verification Planning
   ├── Create checklist from spec requirements
   ├── Define quality gates per category
   └── Identify testing strategy per requirement

4. Risk Assessment
   ├── Identify technical risks
   ├── Assess impact and likelihood
   └── Define mitigation strategies

5. Present for Approval
   ├── Architecture overview
   ├── Task breakdown
   ├── Risk assessment
   └── Request user review
```

### Exit Conditions
- `plan.md` exists with complete task breakdown
- `checklist.md` exists with verification items
- User has explicitly approved the plan
- `workflow-state.md` updated: `plan_approved: true`

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Spec gap discovered | Design reveals missing requirement | Return to Plan phase, update spec |
| Technology conflict | Stack choice conflicts with constraints | Propose alternatives, update plan |
| Plan rejected | Architecture doesn't meet requirements | Revise based on feedback, re-present |

---

## Phase 3: Implement

### Purpose
Execute the approved plan, producing working code that satisfies the specification.

### Entry Conditions
- `plan_approved: true` in workflow state
- `plan.md` and `spec.md` are accessible

### Process

```
1. Execute Tasks in Order
   ├── Follow task sequence from plan.md
   ├── Implement one task at a time
   ├── Verify each task against its spec reference
   └── Update workflow state with progress

2. Deviation Management
   ├── If implementation conflicts with plan → halt and report
   ├── If plan gap discovered → document and consult user
   └── If spec ambiguity found → document and request clarification

3. Continuous Self-Check
   ├── Does the code match the spec requirement?
   ├── Does the architecture match the plan?
   ├── Are error cases handled as specified?
   └── Is the code consistent with project conventions?
```

### Exit Conditions
- All tasks from `plan.md` are implemented
- No known spec deviations
- `workflow-state.md` updated: `impl_complete: true`

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Plan infeasible | Task cannot be implemented as designed | Halt, document issue, revise plan |
| Spec conflict | Requirements contradict during implementation | Halt, flag to user, update spec |
| External dependency failure | Third-party API/library issue | Document in log-error, find alternative |

---

## Phase 4: Review

### Purpose
Systematic code review against specification, quality standards, and security requirements.

### Entry Conditions
- `impl_complete: true` in workflow state
- Implementation code exists
- `spec.md` is accessible for compliance checking

### Process

```
1. Spec Compliance Review
   ├── Check each functional requirement
   ├── Verify acceptance criteria are met
   └── Flag unimplemented or partially implemented requirements

2. Code Quality Review
   ├── SOLID principle compliance
   ├── DRY — no unnecessary duplication
   ├── Naming clarity and consistency
   ├── Error handling completeness
   └── No dead code or unused imports

3. Security Review
   ├── Input validation at boundaries
   ├── Injection prevention (SQL, XSS, command)
   ├── Authentication and authorization
   ├── Sensitive data handling
   └── Dependency vulnerability check

4. Architecture Review
   ├── File structure matches plan
   ├── Component boundaries respected
   ├── Dependencies flow correctly
   └── No circular dependencies

5. Finding Resolution
   ├── Critical/Major findings must be fixed
   ├── Fix code and re-verify the finding
   └── Minor findings documented for future
```

### Exit Conditions
- No critical or major findings remaining
- All spec requirements verified (pass/fail/partial)
- `workflow-state.md` updated: `review_passed: true`

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Critical security issue | Vulnerability in implementation | Fix immediately, re-review affected area |
| Spec non-compliance | Requirement not properly implemented | Return to Implement for the specific requirement |
| Architecture violation | Implementation deviates from plan | Assess impact, fix or update plan with justification |

---

## Phase 5: Test

### Purpose
Verify implementation through spec-mapped testing. Prove that every requirement is satisfied.

### Entry Conditions
- `review_passed: true` in workflow state
- Implementation code exists and has passed review

### Process

```
1. Test Matrix Generation
   ├── Parse spec.md for requirements and acceptance criteria
   ├── Generate test cases per requirement
   ├── Classify: unit, integration, e2e
   └── Identify edge cases and boundary conditions

2. Test Implementation
   ├── Write test code using project's test framework
   ├── Ensure each test references its spec requirement
   └── Include positive and negative test cases

3. Test Execution
   ├── Run full test suite
   ├── Capture pass/fail per test
   ├── Measure coverage against spec (not just code)
   └── Record execution time

4. Failure Analysis
   ├── Classify: test bug vs. implementation bug
   ├── Fix test bugs directly
   ├── Document implementation bugs → return to Implement
   └── Flag spec ambiguities → return to Plan

5. Verification Report
   ├── Requirement coverage matrix
   ├── Test results summary
   ├── Uncovered requirements list
   └── Recommendations
```

### Exit Conditions
- All spec-mapped tests passing
- Verification report generated
- No implementation bugs remaining
- `workflow-state.md` updated: `tests_passed: true`

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Implementation bug found | Test reveals incorrect behavior | Fix in implementation, re-run tests |
| Missing test coverage | Spec requirement has no test | Generate additional tests |
| Flaky test | Non-deterministic test behavior | Fix test isolation, remove external dependencies |

---

## Phase 6: Commit

### Purpose
Finalize the work with structured commits, linked issues, and complete documentation.

### Entry Conditions
- `tests_passed: true` in workflow state
- All tests passing
- No unresolved blockers

### Process

```
1. Change Analysis
   ├── Review all modified files
   ├── Group changes into logical units
   └── Map changes to spec requirements

2. Issue Creation
   ├── Create GitHub issues for each logical unit
   ├── Link to spec requirements
   ├── Add appropriate labels
   └── Include implementation summary

3. Commit Generation
   ├── Generate conventional commit messages
   ├── Reference issue numbers
   ├── Include spec references
   └── Stage and commit changes

4. Workflow Completion
   ├── Update workflow-state.md: committed: true
   ├── Record final artifact inventory
   └── Produce completion summary
```

### Exit Conditions
- All changes committed with structured messages
- GitHub issues created and linked
- `workflow-state.md` updated: `committed: true`
- Workflow cycle complete

### Failure Modes

| Failure | Cause | Recovery |
|---------|-------|----------|
| Commit hook failure | Pre-commit check rejects changes | Fix the issue flagged by the hook |
| Merge conflict | Upstream changes conflict | Resolve conflicts, re-run tests, re-commit |
| Issue creation failure | GitHub API error | Retry or create issues manually |
