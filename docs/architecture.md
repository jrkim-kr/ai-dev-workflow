# Architecture

## System Overview

`ai-dev-workflow` is a document-driven workflow framework. It has no runtime, no server, no database. It's a set of skill definitions, document templates, and conventions that enforce process discipline through AI coding assistants.

```
┌──────────────────────────────────────────────────────────┐
│                    User / Developer                       │
│                                                          │
│    /dev-workflow plan → design → impl → review → test → commit
│                                                          │
└────────────────────────┬─────────────────────────────────┘
                         │
                         ▼
┌──────────────────────────────────────────────────────────┐
│                  AI Coding Assistant                      │
│                                                          │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │
│  │  Skill:     │  │  Skill:     │  │  Skill:     │     │
│  │  dev-wf     │  │  test-ver   │  │  issue-cm   │     │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘     │
│         │                │                │              │
│         ▼                ▼                ▼              │
│  ┌──────────────────────────────────────────────┐       │
│  │           State Engine (workflow-state.md)     │       │
│  │                                                │       │
│  │  Phase transitions │ Entry/exit validation     │       │
│  │  Artifact tracking │ Blocker management        │       │
│  └──────────────────────────────────────────────┘       │
│                                                          │
└────────────────────────┬─────────────────────────────────┘
                         │
                         ▼
┌──────────────────────────────────────────────────────────┐
│                  Project Artifacts                        │
│                                                          │
│  spec.md → plan.md → code → review-report → tests → commits
│                                                          │
│  ┌────────────────────────────────────────┐              │
│  │         workflow-state.md               │              │
│  │  (persistent state across all phases)   │              │
│  └────────────────────────────────────────┘              │
└──────────────────────────────────────────────────────────┘
```

---

## Component Architecture

### 1. Skills Layer

Skills are instruction sets that AI assistants follow. They define behavior, not code. Each skill specifies:

- **Trigger conditions:** When the skill activates
- **Input requirements:** What state and files must exist
- **Actions:** What the skill does (in natural language, executed by AI)
- **Output artifacts:** What the skill produces
- **Validation criteria:** How to verify the skill succeeded

Skills are composable. The `dev-workflow` skill orchestrates other skills (`spec-writer`, `code-review`, `test-verify`, `issue-commit`) across the six phases.

```
┌─────────────────────────────────────────┐
│             dev-workflow                  │
│          (orchestrator skill)            │
│                                          │
│  Phase 1 ──→ spec-writer                │
│  Phase 2 ──→ (inline design logic)      │
│  Phase 3 ──→ (inline implementation)    │
│  Phase 4 ──→ code-review                │
│  Phase 5 ──→ test-verify                │
│  Phase 6 ──→ issue-commit               │
│                                          │
│  Cross-cutting: log-error               │
└─────────────────────────────────────────┘
```

### 2. State Layer

The state layer is a single markdown file (`workflow-state.md`) that tracks:

- **Current phase:** Which phase is active
- **Phase status:** pending, in_progress, completed, blocked
- **Transition log:** History of phase transitions with timestamps
- **Artifact registry:** Which documents exist and their status
- **Flags:** Boolean gates that control phase transitions
- **Blockers:** Issues preventing progress

The state file is the enforcement mechanism. Skills read it before acting and write to it after acting. Phase transitions only occur when the state file permits them.

### 3. Template Layer

Templates define the structure of project artifacts. They ensure consistency across projects and provide AI assistants with clear output formats.

| Template | Purpose | Used In Phase |
|----------|---------|---------------|
| `spec.md` | Requirement specification | Plan |
| `plan.md` | Implementation plan | Design |
| `checklist.md` | Verification checklist | Design, Review |
| `workflow-state.md` | State tracking | All phases |

Templates are opinionated. They include specific sections, required fields, and formatting conventions. This is intentional — structured documents are easier for both humans and AI to work with.

### 4. Documentation Layer

Documentation explains the why behind the framework's decisions. It serves two audiences:

- **Users:** Engineers who want to understand the workflow before adopting it
- **Contributors:** Engineers who want to improve or extend the framework

---

## Data Flow

```
User Input (requirements, feedback, approvals)
    │
    ▼
┌──────────┐     ┌──────────┐     ┌──────────┐
│  Plan    │────▶│  Design  │────▶│  Impl    │
│          │     │          │     │          │
│ spec.md  │     │ plan.md  │     │ code     │
│          │     │ check.md │     │          │
└──────────┘     └──────────┘     └──────────┘
                                       │
    ┌──────────────────────────────────┘
    │
    ▼
┌──────────┐     ┌──────────┐     ┌──────────┐
│  Review  │────▶│  Test    │────▶│  Commit  │
│          │     │          │     │          │
│ findings │     │ tests    │     │ commits  │
│          │     │ report   │     │ issues   │
└──────────┘     └──────────┘     └──────────┘

State file updated at every transition ──────────▶ workflow-state.md
```

Each phase reads artifacts from previous phases and produces new artifacts. The state file is the only mutable document that spans the entire workflow.

---

## Design Decisions

### Why Markdown, Not YAML/JSON/TOML?

Markdown is:
- Human-readable without tooling
- Editable in any text editor
- Renderable on GitHub and most documentation platforms
- Parseable by AI assistants without special instructions
- Diffable in version control

Structured data (like the state file's flags) uses YAML code blocks within markdown, giving us the best of both worlds.

### Why Sequential, Not Parallel?

Parallel phase execution creates race conditions in the requirement space. If implementation starts before design is approved, design changes invalidate implementation work. Sequential execution eliminates this class of problems entirely.

Future versions may support controlled parallelism for independent sub-tasks within a phase.

### Why Document-Driven, Not Tool-Driven?

Documents are portable, version-controllable, and AI-assistant-agnostic. A tool-driven approach would tie the workflow to a specific runtime or platform. Documents work with any AI assistant that can read and write files.

### Why No Configuration in v0.1?

Configuration adds complexity. Before making things configurable, we need to validate that the default workflow works well for real projects. Configuration will be introduced in v0.2 after gathering user feedback on what actually needs to be customizable.
