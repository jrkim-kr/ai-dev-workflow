# dev-workflow — Master Orchestrator

Manages the 6-phase sequential workflow: Plan → Design → Implement → Review → Test → Commit. Validates each phase's completion before allowing progression. **Does not perform work directly** (except Phase 3) — delegates to specialized skills.

## Workflow

```
Phase 1        Phase 2        Phase 3        Phase 4        Phase 5        Phase 6
Plan        →  Design      →  Implement   →  Review      →  Test        →  Commit
─────────      ─────────      ─────────      ─────────      ─────────      ─────────
spec.md        plan.md        Write code     Code review    Write tests    Create issues
system-        checklist.md   Step-by-step   SOLID/Security Run tests      Commit
design.md                     impl           Quality scan   Verification   Push (opt.)
migrations/                                  P0-P3 rating   report
```

## Orchestrator Role

| Responsibility | `/dev-workflow` | Delegated Skill |
|---------------|:-:|:-:|
| Prerequisite validation | O | X |
| Actual work execution | X (except Phase 3) | O |
| workflow-state.md update | O | X |
| Standalone execution | X (workflow only) | O |

## State Tracking

State recorded in `docs/plan/workflow-state.md`. If missing, starts from Phase 1.

## Commands

| Command | Phase | Delegates To | Output |
|---------|-------|-------------|--------|
| `status` (or no arg) | — | — | Current workflow status report |
| `plan` | 1. Plan | `/web-project-planner plan` | spec.md, system-design.md, migrations/ |
| `design` | 2. Design | `/web-project-planner design` | plan.md, checklist.md |
| `impl [step]` | 3. Implement | — (self) | Source code |
| `review` | 4. Review | `/code-review-expert` + spec compliance | Review report (P0–P3) |
| `test` | 5. Test | `/test-verify all` | Test files, verification report |
| `commit` | 6. Commit | `/issue-commit` | GitHub issues, git commit, push |

### Phase 1: `plan`

**Prerequisites:** None
1. Execute `/web-project-planner plan` (Q&A → spec.md, system-design.md, migrations)
2. Update workflow-state.md (Phase 1 = completed)
3. Verify: `docs/spec/spec.md`, `docs/architecture/system-design.md`, `docs/schema/` exist

### Phase 2: `design`

**Prerequisites:** Phase 1 complete
1. Execute `/web-project-planner design` (reads existing spec → plan.md, checklist.md)
2. Update workflow-state.md (Phase 2 = completed)
3. Verify: `docs/plan/plan.md`, `docs/plan/checklist.md` exist

### Phase 3: `impl [step]`

**Prerequisites:** Phase 2 complete
1. Read plan.md → identify current step
2. Read checklist.md → identify check items
3. Read spec.md → reference detailed spec
4. Write/modify code per step's file list
5. Mark completed items as `[x]` in checklist.md
6. Update workflow-state.md with progress

**Rules:** Step-by-step execution. Report after each step. If specific step number given, implement only that step. On error, use `/log-error` format.

### Phase 4: `review`

**Prerequisites:** At least 1 step implemented
1. **Spec compliance review** (orchestrator's own task): compare spec.md vs. implementation
2. **Code quality review:** delegate to `/code-review-expert` (SOLID, security, P0–P3)
3. P0/P1 found → guide to fix and re-review. No P0/P1 → Phase 4 = completed

### Phase 5: `test`

**Prerequisites:** Phase 4 complete
1. Execute `/test-verify all`
2. PASS → Phase 5 = completed. CONDITIONAL_PASS → confirm with user. FAIL → guide to fix

### Phase 6: `commit`

**Prerequisites:** Phase 5 complete
1. Execute `/issue-commit` (git pull → error docs → GitHub issues → commit → push)
2. Update workflow-state.md (Phase 6 = completed)
3. Produce completion report

## Rules

1. **No skipping phases.** Block if prior phase is incomplete.
2. **Mandatory state updates.** Update workflow-state.md at every phase transition.
3. **Incremental progress.** Confirm with user between phases.
4. **git pull first.** Sync latest code before every phase.
