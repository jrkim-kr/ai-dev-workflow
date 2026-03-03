# dev-workflow

**Core orchestrator skill for the AI-native development workflow.**

Manages the 6-phase sequential development process: Plan → Design → Implement → Review → Test → Commit. Enforces phase ordering, validates transition conditions, and maintains workflow state.

---

## Purpose

Provide deterministic, spec-driven project execution by enforcing strict phase sequencing. Prevents AI coding assistants from jumping between tasks without completing prerequisite phases.

---

## Trigger

Activated by `/dev-workflow` command with a phase argument:

```
/dev-workflow plan
/dev-workflow design
/dev-workflow impl
/dev-workflow review
/dev-workflow test
/dev-workflow commit
```

---

## Phase Definitions

### Phase 1: Plan

**Objective:** Produce a complete project specification.

**Actions:**
1. Analyze project requirements (user input, existing codebase, constraints)
2. Generate `spec.md` using the specification template
3. Include: functional requirements, non-functional requirements, data models, API contracts, acceptance criteria
4. Present spec to user for review and approval

**Entry conditions:** None (initial phase)
**Exit conditions:** User approves `spec.md`
**Output artifact:** `spec.md`

### Phase 2: Design

**Objective:** Produce a system design and implementation plan.

**Actions:**
1. Read approved `spec.md`
2. Generate `plan.md` with: architecture decisions, file structure, component breakdown, task sequencing, dependency mapping
3. Generate `checklist.md` with verification items mapped to spec requirements
4. Present design to user for review and approval

**Entry conditions:** `spec_approved: true` in workflow state
**Exit conditions:** User approves `plan.md`
**Output artifacts:** `plan.md`, `checklist.md`

### Phase 3: Implement

**Objective:** Execute the approved implementation plan.

**Actions:**
1. Read `plan.md` and execute tasks in order
2. Track implementation progress in `workflow-state.md`
3. Flag any deviations from spec immediately
4. Halt and consult user if implementation conflicts with approved design

**Entry conditions:** `plan_approved: true` in workflow state
**Exit conditions:** All planned tasks complete, no known spec deviations
**Output artifacts:** Implementation code, updated `workflow-state.md`

### Phase 4: Review

**Objective:** Structured code review against specification.

**Actions:**
1. Compare implementation against `spec.md` requirements point by point
2. Check for: SOLID violations, security risks, error handling gaps, edge cases, naming consistency
3. Produce review findings with severity ratings (critical, major, minor)
4. Fix all critical and major findings before proceeding

**Entry conditions:** `impl_complete: true` in workflow state
**Exit conditions:** No critical or major review findings remaining
**Output artifacts:** Review report, code fixes

### Phase 5: Test

**Objective:** Spec-mapped test generation and execution.

**Actions:**
1. Read `spec.md` acceptance criteria
2. Generate test cases: unit tests, integration tests, e2e tests as appropriate
3. Map every functional requirement to at least one test case
4. Execute tests and produce verification report
5. Fix failing tests (distinguish between test bugs and implementation bugs)

**Entry conditions:** `review_passed: true` in workflow state
**Exit conditions:** All spec-mapped tests passing
**Output artifacts:** Test files, verification report

### Phase 6: Commit

**Objective:** Structured commit with full traceability.

**Actions:**
1. Generate commit message following conventional commit format
2. Create GitHub issues for completed work items
3. Link commits to issues
4. Update `workflow-state.md` to mark workflow complete
5. Produce summary of all artifacts created

**Entry conditions:** `tests_passed: true` in workflow state
**Exit conditions:** Code committed, issues created
**Output artifacts:** Git commits, GitHub issues, final `workflow-state.md`

---

## State Management

The skill reads and writes `workflow-state.md` to track progress:

```yaml
project: <project-name>
current_phase: plan | design | implement | review | test | commit
spec_approved: true | false
plan_approved: true | false
design_approved: true | false
impl_complete: true | false
review_passed: true | false
tests_passed: true | false
committed: true | false
started_at: <ISO timestamp>
last_updated: <ISO timestamp>
```

**State transitions are one-directional.** A phase cannot be "unapproved" once approved. If requirements change mid-workflow, a new workflow cycle begins.

---

## Error Handling

- **Missing prerequisite:** If a phase is invoked without its entry conditions met, the skill reports which conditions are unmet and which phase must be completed first.
- **State file missing:** If `workflow-state.md` doesn't exist and a non-Plan phase is invoked, the skill creates an initial state and directs the user to start with Plan.
- **Spec deviation detected:** During implementation, if the AI detects a conflict between the plan and what's feasible, it halts and reports the deviation for user decision.

---

## Configuration

Future versions will support `.dev-workflow.yaml` for:
- Phase customization (skip/combine phases for specific project types)
- Custom validation hooks
- Team-specific review criteria
- Integration with external tools

Currently, the workflow operates with default settings as documented above.
