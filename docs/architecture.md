# Architecture

## Overview

`ai-dev-workflow` is a document-driven workflow framework with no runtime, no server, no database. It's a set of skill definitions and conventions that enforce process discipline through AI coding assistants.

**Core belief:** Speed without structure is not productivity — it's technical debt at machine scale. This framework trades some speed for significant reliability. Each phase prevents a category of problems that would cost more time to fix later.

## System Diagram

```
┌──────────────────────────────────────────────────────────┐
│  User: /dev-workflow plan → design → impl → review → test → commit
└────────────────────────┬─────────────────────────────────┘
                         ▼
┌──────────────────────────────────────────────────────────┐
│  Orchestrator (dev-workflow)                              │
│  Validates prerequisites → Delegates → Updates state     │
│                                                          │
│  Phase 1+2 ──→ web-project-planner                      │
│  Phase 3   ──→ (self)                                   │
│  Phase 4   ──→ code-review-expert + spec compliance     │
│  Phase 5   ──→ test-verify                              │
│  Phase 6   ──→ issue-commit                             │
│  Any phase ──→ log-error (cross-cutting)                │
└────────────────────────┬─────────────────────────────────┘
                         ▼
┌──────────────────────────────────────────────────────────┐
│  Project Artifacts                                       │
│                                                          │
│  docs/spec/spec.md                                      │
│  docs/architecture/system-design.md                     │
│  docs/schema/migrations/*.sql                           │
│  docs/plan/plan.md                                      │
│  docs/plan/checklist.md                                 │
│  docs/plan/workflow-state.md  ← persistent state        │
│  source code, tests, commits                            │
└──────────────────────────────────────────────────────────┘
```

## Data Flow

```
User Requirements
    │
    ▼
┌──────────┐     ┌──────────┐     ┌──────────┐
│  Plan    │────▶│  Design  │────▶│  Impl    │
│ spec.md  │     │ plan.md  │     │  code    │
│ sys-des. │     │ check.md │     │          │
│ migrate  │     │          │     │          │
└──────────┘     └──────────┘     └──────────┘
                                       │
    ┌──────────────────────────────────┘
    ▼
┌──────────┐     ┌──────────┐     ┌──────────┐
│  Review  │────▶│  Test    │────▶│  Commit  │
│ P0-P3    │     │ tests    │     │ issues   │
│ findings │     │ report   │     │ commits  │
└──────────┘     └──────────┘     └──────────┘

State updated at every transition → docs/plan/workflow-state.md
```

## Design Decisions

**Why Markdown?** Human-readable, editable anywhere, renderable on GitHub, parseable by AI, diffable in git.

**Why sequential?** Parallel phases create race conditions in requirements space. If implementation starts before design is approved, design changes invalidate work.

**Why document-driven?** Documents are portable, version-controllable, and AI-assistant-agnostic. Not tied to any specific runtime or platform.

**Why the orchestrator delegates?** Separation of concerns. The orchestrator handles state and sequencing. Specialized skills handle actual work. Each skill is also independently usable outside the workflow.
