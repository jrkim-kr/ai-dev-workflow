---
name: dev-workflow
description: "Project development workflow orchestrator. Manages 6 sequential phases: Planning → Design → Implementation → Review → Testing → Commit. Verifies completion of each phase before proceeding to the next."
argument-hint: "[plan | design | impl | review | test | commit | status]"
user-invokable: true
---

# Development Workflow Orchestrator

## Purpose

Manage the 6-phase workflow of **Planning → Design → Implementation → Review → Testing → Commit** for new projects or new features. Verify that each phase is complete before proceeding to the next, ensuring systematic and thorough development.

## Full Workflow Overview

```
Phase 1        Phase 2        Phase 3        Phase 4        Phase 5        Phase 6
Plan        →  Design      →  Implement   →  Review      →  Test        →  Commit
─────────     ─────────      ─────────      ─────────      ─────────      ─────────
spec.md       plan.md        Write code     Code review    Write tests    Create issues
system-       checklist.md   Step-by-step   SOLID/Security Run tests      Commit
design.md                    implementation Quality check   Verification   Push (optional)
migrations/                                                report
```

## State Tracking

Workflow state is recorded in `docs/plan/workflow-state.md`. If this file doesn't exist, start from Phase 1.

### workflow-state.md Format

```markdown
# Workflow State

| Phase | Status | Completion Date |
|-------|--------|-----------------|
| 1. Planning | completed / in_progress / pending | YYYY-MM-DD |
| 2. Design | ... | ... |
| 3. Implementation | ... | ... |
| 4. Review | ... | ... |
| 5. Testing | ... | ... |
| 6. Commit | ... | ... |

## Current Phase: N
## Current Step: (detailed step if Phase 3)
## Last Updated: YYYY-MM-DD
```

---

## Behavior by Argument

### No argument or `status`

1. Read `docs/plan/workflow-state.md`
2. If file doesn't exist: treat as new project and guide to Phase 1
3. If file exists: summarize current phase status and next action

Output format:
```
## Workflow Status

| Phase | Status | Notes |
|-------|--------|-------|
| 1. Planning | ✅ Complete | 2026-02-28 |
| 2. Design | 🔄 In Progress | Working on plan.md |
| 3~6 | ⬜ Pending | |

**Next action:** Run `/dev-workflow design` to complete plan.md
```

---

### `plan` — Phase 1: Planning

**Purpose:** Delegate to `/web-project-planner plan` skill to generate planning documents.

#### Prerequisites
- None (first phase)

#### Execution Steps

1. Run `/web-project-planner plan` skill (Q&A → generate spec.md, system-design.md, migrations)
2. Update `workflow-state.md` (Phase 1 = completed)

#### Completion Criteria
- [ ] `docs/spec/spec.md` exists
- [ ] `docs/architecture/system-design.md` exists
- [ ] `docs/schema/` exists (if DB is used)

---

### `design` — Phase 2: Design

**Purpose:** Delegate to `/web-project-planner design` skill to generate implementation plan and checklist.

#### Prerequisites
- Phase 1 complete (spec.md, system-design.md exist)

#### Execution Steps

1. Run `/web-project-planner design` skill (generate plan.md, checklist.md based on existing spec.md)
2. Update `workflow-state.md` (Phase 2 = completed)

#### Completion Criteria
- [ ] `docs/plan/plan.md` exists
- [ ] `docs/plan/checklist.md` exists
- [ ] All features from spec.md are included in plan.md

---

### `impl` or `impl [step]` — Phase 3: Implementation

**Purpose:** Implement steps from plan.md in order.

#### Prerequisites
- Phase 2 complete (plan.md, checklist.md exist)

#### Execution Steps

1. Read `docs/plan/plan.md` → identify current step
2. Read `docs/plan/checklist.md` → check items for that step
3. Read `docs/spec/spec.md` → get detailed spec for the feature
4. Write/modify code according to step's file list
5. After implementation, check off corresponding items in checklist.md with `[x]`
6. Update `docs/plan/workflow-state.md` (record current step)

#### Implementation Rules
- Implement step by step, reporting to user after each step completion
- If a specific step number is given as argument, implement only that step
- If no argument, proceed sequentially from the next incomplete step
- Document errors in `errors/ERR-NNN.md` format when they occur

#### Step Completion Report Format
```
## Step [N] Complete

### Created/Modified Files
- `src/...` — description

### Checklist Progress
- [x] #N item description
- [x] #N+1 item description

### Next Step
Step [N+1]: [name] → `/dev-workflow impl [N+1]`
```

---

### `review` — Phase 4: Code Review

**Purpose:** Delegate to `/code-review-expert` skill for code quality review, plus perform spec comparison.

#### Prerequisites
- At least 1 step completed in Phase 3

#### Execution Steps

1. **Spec comparison review:** (workflow-specific — not part of `/code-review-expert`)
   - Compare `docs/spec/spec.md` against actual implementation
   - List missing features and discrepancies:

```markdown
### spec.md Comparison
| Feature | Implementation Status | Notes |
|---------|----------------------|-------|
| ... | ✅ / ❌ / ⚠️ | ... |
```

2. **Code quality review:** Run `/code-review-expert` skill (delegates SOLID, security, code quality, P0~P3 classification entirely)
3. Overall assessment:
   - **P0/P1 issues exist**: Guide to fix and re-review
   - **No P0/P1 issues**: Update `workflow-state.md` (Phase 4 = completed), guide to Phase 5

---

### `test` — Phase 5: Testing & Verification

**Purpose:** Delegate to `/test-verify` skill to generate test cases, run them, and verify the implementation.

#### Prerequisites
- Phase 4 complete (no P0/P1 issues)

#### Execution Steps

1. **Check test infrastructure:** Verify test framework is installed
   - If not installed, `/test-verify` will guide automatic installation
2. Run `/test-verify all` skill. test-verify performs:
   - Test environment setup (framework installation, fixture creation)
   - Map checklist.md items to assertions for test case generation
   - Test worker internal logic via pure function isolation
   - Auto-verify performance criteria via benchmark tests
   - Run tests and generate verification report
3. Check verification report's overall verdict:
   - **PASS**: Update `workflow-state.md` (Phase 5 = completed), guide to Phase 6
   - **CONDITIONAL_PASS**: P2 or lower issues within 10% of total (or 5 or fewer), and core pipeline passes — share list with user and confirm whether to proceed
   - **FAIL**: Guide to fix failures and re-verify with `/test-verify verify`

#### Phase 5 Artifacts
- Test files: `*.test.ts`, `*.bench.ts`
- Test fixtures: `__fixtures__/` or `__testdata__/`
- Manual verification guide: `docs/test/manual-verification.md`
- Verification report: `docs/test/verification-report.md`

---

### `commit` — Phase 6: Commit & Completion

**Purpose:** Delegate to `/issue-commit` skill to organize changes and commit.

#### Prerequisites
- Phase 5 complete (tests passed)

#### Execution Steps

1. Run `/issue-commit` skill (delegates git pull, error documentation, GitHub issue creation, and commit)
2. Update `workflow-state.md` (Phase 6 = completed)
3. Workflow completion report: overall phase completion dates, generated documents, commit info, next steps guidance

---

## Common Rules

1. **Phase skipping is prohibited**: Cannot proceed to next phase if previous phase is incomplete. Guide user to complete current phase.
2. **State file must be updated**: Always update `workflow-state.md` when each phase starts/completes.
3. **Follow related skill conventions**: Each phase follows the format and rules of its associated skill.
4. **Incremental progress**: Do not run all phases at once. Confirm with user after each phase before proceeding.
5. **git pull first**: Sync latest code with `git pull` before starting any phase.

## Related Skill Mapping

| Phase | Delegated Skill | Role |
|-------|----------------|------|
| 1+2. Planning & Design | `web-project-planner` | **Delegate**: Q&A, spec/design/plan/checklist generation |
| 3. Implementation | — (self-executing) | **Direct**: Step-by-step implementation with error documentation |
| 4. Review | `code-review-expert` | **Delegate**: SOLID/security/quality review (+ spec comparison done by workflow itself) |
| 5. Testing | `test-verify` | **Delegate**: Test generation/execution/verification report |
| 6. Commit | `issue-commit` | **Delegate**: git pull, issue creation, commit |
