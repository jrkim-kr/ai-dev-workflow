# Workflow Diagrams

Visual representations of the `ai-dev-workflow` process, state transitions, and architecture.

---

## 1. Phase Sequence

The core 6-phase sequential workflow:

```
  ╔══════════╗     ╔══════════╗     ╔══════════╗
  ║   PLAN   ║────▶║  DESIGN  ║────▶║   IMPL   ║
  ║          ║     ║          ║     ║          ║
  ║ spec.md  ║     ║ plan.md  ║     ║   code   ║
  ╚══════════╝     ╚══════════╝     ╚══════════╝
                                         │
  ╔══════════╗     ╔══════════╗          │
  ║  COMMIT  ║◀────║   TEST   ║◀─────────┘
  ║          ║     ║          ║     ╔══════════╗
  ║ commits  ║     ║  tests   ║◀────║  REVIEW  ║
  ║ issues   ║     ║ report   ║     ║          ║
  ╚══════════╝     ╚══════════╝     ║ findings ║
                                     ╚══════════╝
```

**Rule: Each phase must complete and receive approval before the next phase begins.**

---

## 2. State Machine

Phase transitions and their conditions:

```
                    ┌─────────────────────────────┐
                    │                             │
                    ▼                             │
  ┌────────┐  approve  ┌────────┐  approve  ┌────────┐
  │  PLAN  │─────────▶│ DESIGN │─────────▶│  IMPL  │
  │        │          │        │          │        │
  └───┬────┘          └───┬────┘          └───┬────┘
      │                   │                   │
      │ reject            │ reject            │ deviation
      │                   │                   │   detected
      ▼                   ▼                   ▼
  ┌────────┐          ┌────────┐          ┌────────┐
  │ REVISE │          │ REVISE │          │  HALT  │
  │  SPEC  │──loop──▶│  PLAN  │──loop──▶│ CONSULT│
  └────────┘          └────────┘          └────────┘

  ┌────────┐   pass   ┌────────┐   pass   ┌────────┐
  │ REVIEW │────────▶│  TEST  │────────▶│ COMMIT │
  │        │          │        │          │        │
  └───┬────┘          └───┬────┘          └────────┘
      │                   │
      │ findings          │ failures
      │                   │
      ▼                   ▼
  ┌────────┐          ┌────────┐
  │  FIX   │          │  FIX   │
  │  CODE  │──loop──▶│ CODE/  │──loop──▶
  └────────┘          │ TESTS  │
                      └────────┘
```

---

## 3. Artifact Flow

How documents flow through the workflow:

```
  User Requirements
        │
        ▼
  ┌─────────────┐
  │   spec.md   │◄──── Source of truth for all downstream artifacts
  └──────┬──────┘
         │
         ├──────────────────────────────────────────────┐
         │                                              │
         ▼                                              │
  ┌─────────────┐                                       │
  │   plan.md   │◄──── Architecture + task breakdown    │
  └──────┬──────┘                                       │
         │                                              │
         ├─────────────────┐                            │
         │                 │                            │
         ▼                 ▼                            │
  ┌─────────────┐  ┌──────────────┐                    │
  │    code     │  │ checklist.md │                    │
  └──────┬──────┘  └──────────────┘                    │
         │                                              │
         ▼                                              │
  ┌─────────────┐         spec.md ──────────────────────┘
  │   review    │◄──── Reviewed against spec.md
  │   report    │
  └──────┬──────┘
         │
         ▼
  ┌─────────────┐
  │   tests     │◄──── Generated from spec.md
  │   report    │      acceptance criteria
  └──────┬──────┘
         │
         ▼
  ┌─────────────┐
  │  commits    │
  │  issues     │
  └─────────────┘
```

**Key insight:** The specification is referenced in Plan, Review, and Test phases. It's the constant against which everything else is validated.

---

## 4. Skill Orchestration

How the orchestrator skill delegates to specialized skills:

```
                     /dev-workflow
                          │
            ┌─────────────┼─────────────┐
            │             │             │
            ▼             ▼             ▼
     ┌────────────┐ ┌──────────┐ ┌──────────┐
     │ spec-writer│ │code-review│ │test-verify│
     │            │ │          │ │          │
     │ Phase 1    │ │ Phase 4  │ │ Phase 5  │
     └────────────┘ └──────────┘ └──────────┘
                                       │
            ┌──────────────────────────┘
            │
            ▼
     ┌────────────┐     ┌────────────┐
     │issue-commit│     │ log-error  │
     │            │     │            │
     │ Phase 6    │     │ Any phase  │
     └────────────┘     └────────────┘

  ─────────────────────────────────────────
  │         workflow-state.md              │
  │    (shared state across all skills)    │
  ─────────────────────────────────────────
```

---

## 5. Decision Points

Where human judgment is required:

```
  Phase 1: PLAN
  │
  ├──▶ DECISION: Is the spec complete and accurate?
  │    ├── YES → proceed to Design
  │    └── NO  → revise spec (provide feedback)
  │
  Phase 2: DESIGN
  │
  ├──▶ DECISION: Is the architecture sound?
  │    ├── YES → proceed to Implement
  │    └── NO  → revise plan (provide feedback)
  │
  Phase 3: IMPLEMENT
  │
  ├──▶ DECISION: Is a spec deviation acceptable?
  │    ├── YES → update spec + continue
  │    └── NO  → revert deviation + follow spec
  │
  Phase 4: REVIEW
  │
  ├──▶ AUTO: Critical/major findings block progress
  │         Minor findings are documented
  │
  Phase 5: TEST
  │
  ├──▶ AUTO: All spec-mapped tests must pass
  │         Failures return to implementation
  │
  Phase 6: COMMIT
  │
  └──▶ DECISION: Approve commit messages and issues
       ├── YES → commit and close workflow
       └── NO  → revise messages
```

Human decisions happen at phase boundaries. Within phases, the workflow enforces rules automatically.

---

## 6. Error Recovery Paths

What happens when things go wrong:

```
  Normal flow:  PLAN → DESIGN → IMPL → REVIEW → TEST → COMMIT
                  │       │       │       │       │
  Recovery:       │       │       │       │       │
                  │       │       │       │       └──▶ Fix tests → re-run
                  │       │       │       │
                  │       │       │       └──▶ Fix findings → re-review
                  │       │       │
                  │       │       └──▶ Spec deviation → halt + consult
                  │       │                              │
                  │       │                              ├──▶ Update spec → restart from Plan
                  │       │                              └──▶ Adjust impl → continue
                  │       │
                  │       └──▶ Spec gap found → return to Plan
                  │
                  └──▶ Spec rejected → revise and re-present

  Cross-cutting:
  ┌────────────────────────────────────────────────────────┐
  │  log-error: captures errors at any phase               │
  │  Creates docs/errors/ entries for institutional memory  │
  └────────────────────────────────────────────────────────┘
```

Recovery always flows backward to the appropriate phase. There is no "skip and fix later."
