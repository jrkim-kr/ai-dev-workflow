# ai-dev-workflow

**AI writes code. It doesn't enforce discipline. This workflow does.**

An opinionated, spec-driven development framework that enforces process discipline when building with AI coding assistants. Six sequential phases. No skipping. No shortcuts.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

---

## The Problem

AI coding assistants are powerful. They generate code fast. But speed without structure creates chaos:

- **No spec verification.** AI starts coding before requirements are validated. Assumptions propagate silently.
- **No deterministic progression.** Tasks jump between design, implementation, and testing randomly. State is lost.
- **No enforced review phase.** Generated code ships without systematic quality checks against the original spec.
- **No test mapping.** Tests are an afterthought — disconnected from requirements, incomplete by default.
- **No audit trail.** When something breaks, there's no record of what was planned vs. what was built.

The result: AI-assisted projects that start fast and collapse faster. Code that works in isolation but fails as a system. Bugs that trace back to requirements that were never written down.

**This framework exists to fix that.**

---

## Philosophy

`ai-dev-workflow` is built on five non-negotiable principles:

### 1. Spec-Driven

Every project starts with a specification. Not a vague description — a structured document with functional requirements, constraints, and acceptance criteria. AI cannot write code that satisfies requirements that don't exist.

### 2. State-Tracked

The workflow maintains explicit state. You always know which phase you're in, what's been completed, and what's blocking progress. No implicit assumptions. No "I think we're done with design."

### 3. Sequential

Phases execute in strict order: Plan → Design → Implement → Review → Test → Commit. Each phase has entry conditions and exit criteria. You cannot implement before design is approved. You cannot commit before tests pass.

### 4. Review-Mandatory

Every implementation undergoes structured review against the original spec. This is not optional. The review phase checks for spec compliance, code quality, security concerns, and architectural consistency.

### 5. Test-Mapped

Tests are derived directly from the specification. Every functional requirement maps to at least one test case. Coverage is measured against the spec, not just the code.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    ai-dev-workflow                           │
│                                                             │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐       │
│  │  SPEC   │→ │  PLAN   │→ │ DESIGN  │→ │  IMPL   │→ ...  │
│  └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘       │
│       │            │            │            │              │
│       ▼            ▼            ▼            ▼              │
│  ┌─────────────────────────────────────────────────┐       │
│  │             workflow-state.md                     │       │
│  │  ┌──────────────────────────────────────────┐   │       │
│  │  │ phase: implement                         │   │       │
│  │  │ spec_approved: true                      │   │       │
│  │  │ plan_approved: true                      │   │       │
│  │  │ design_approved: true                    │   │       │
│  │  │ impl_complete: false                     │   │       │
│  │  │ review_passed: false                     │   │       │
│  │  │ tests_passed: false                      │   │       │
│  │  └──────────────────────────────────────────┘   │       │
│  └─────────────────────────────────────────────────┘       │
│                                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                  │
│  │  Skills  │  │Templates │  │  Docs    │                  │
│  │          │  │          │  │          │                  │
│  │ dev-wf   │  │ spec.md  │  │ philos.  │                  │
│  │ log-err  │  │ plan.md  │  │ arch.    │                  │
│  │ issue-cm │  │ check.md │  │ lifecyc. │                  │
│  │ test-ver │  │ state.md │  │ review   │                  │
│  └──────────┘  └──────────┘  └──────────┘                  │
└─────────────────────────────────────────────────────────────┘
```

The system consists of three layers:

- **Skills** — Executable workflow phases that enforce sequencing and validation.
- **Templates** — Structured document formats that standardize project artifacts.
- **Docs** — Engineering documentation covering philosophy, architecture, and process.

---

## The Six Phases

### Phase 1: Plan (`/dev-workflow plan`)

Analyze requirements and produce a structured specification (`spec.md`). Define functional requirements, constraints, data models, and acceptance criteria. No implementation details — just what the system must do.

**Entry:** Raw requirements or project brief
**Exit:** `spec.md` reviewed and approved

### Phase 2: Design (`/dev-workflow design`)

Produce system design and implementation plan (`plan.md`). Define architecture, file structure, API contracts, database schema, and component boundaries. Map each spec requirement to concrete implementation tasks.

**Entry:** Approved `spec.md`
**Exit:** `plan.md` reviewed and approved

### Phase 3: Implement (`/dev-workflow impl`)

Execute the implementation plan. Write code following the approved design. Track progress against `plan.md` tasks. Flag deviations from spec immediately.

**Entry:** Approved `plan.md`
**Exit:** All planned tasks implemented, no known spec deviations

### Phase 4: Review (`/dev-workflow review`)

Structured code review against the original specification. Check for: spec compliance, code quality (SOLID, DRY), security vulnerabilities, error handling, edge cases, and architectural consistency.

**Entry:** Implementation complete
**Exit:** All review findings addressed

### Phase 5: Test (`/dev-workflow test`)

Generate and execute test cases derived from `spec.md`. Map every functional requirement to test coverage. Run unit, integration, and e2e tests as appropriate. Produce a verification report.

**Entry:** Review passed
**Exit:** All spec-mapped tests passing, verification report generated

### Phase 6: Commit (`/dev-workflow commit`)

Generate structured commit messages. Create GitHub issues for tracking. Document the completed work with full traceability from spec to implementation to tests.

**Entry:** All tests passing
**Exit:** Code committed with linked issues and documentation

---

## Quick Start

### 1. Install the skills

Copy the skill files from `skills/` into your Claude Code skills directory:

```bash
cp skills/*.md ~/.claude/skills/
```

### 2. Initialize a project

Create a new project directory and initialize the workflow:

```bash
mkdir my-project && cd my-project
```

### 3. Run the workflow

```bash
# Start with planning — analyze requirements, produce spec
/dev-workflow plan

# Design the system — architecture, file structure, API contracts
/dev-workflow design

# Implement — write code following the approved plan
/dev-workflow impl

# Review — structured code review against spec
/dev-workflow review

# Test — spec-mapped test generation and execution
/dev-workflow test

# Commit — structured commits with issue tracking
/dev-workflow commit
```

Each phase validates that the previous phase was completed before proceeding. The workflow state is tracked in `workflow-state.md`.

---

## Project Structure

```
ai-dev-workflow/
│
├── README.md                 # You are here
├── LICENSE                   # MIT License
├── CONTRIBUTING.md           # Contribution guidelines
├── ROADMAP.md                # Version evolution plan
│
├── skills/                   # Executable skill definitions
│   ├── dev-workflow.md       # Core orchestrator skill
│   ├── log-error.md          # Error documentation skill
│   ├── issue-commit.md       # Issue + commit automation
│   ├── test-verify.md        # Test generation + verification
│   ├── code-review.md        # Structured code review
│   └── spec-writer.md        # Specification authoring
│
├── templates/                # Reusable document templates
│   ├── spec.md               # Specification template
│   ├── plan.md               # Implementation plan template
│   ├── checklist.md          # Verification checklist template
│   └── workflow-state.md     # Workflow state tracking template
│
├── docs/                     # Framework documentation
│   ├── philosophy.md         # Design principles and rationale
│   ├── architecture.md       # System architecture deep-dive
│   ├── lifecycle.md          # Phase lifecycle documentation
│   └── spec-review-process.md # Spec review methodology
│
├── examples/                 # Reference implementations
│   └── example-reservation-system/
│       ├── spec.md           # Example specification
│       ├── plan.md           # Example implementation plan
│       ├── workflow-state.md # Example state tracking
│       └── sample-tests.md   # Example test cases
│
└── diagrams/                 # Visual documentation
    └── workflow-overview.md  # Workflow diagrams
```

---

## Roadmap

| Version | Focus | Status |
|---------|-------|--------|
| **v0.1** | Core 6-phase workflow, templates, skill definitions | **Current** |
| **v0.2** | Configurable phase rules, custom validation hooks | Planned |
| **v0.3** | Multi-agent workflow support, parallel phase execution | Planned |
| **v0.5** | Plugin system for custom skills, community skill registry | Planned |
| **v0.7** | CI/CD integration, automated workflow enforcement | Planned |
| **v1.0** | Stable API, comprehensive documentation, ecosystem maturity | Planned |

See [ROADMAP.md](ROADMAP.md) for the detailed evolution plan.

---

## Contributing

This framework is evolving. Your input matters.

- **Found a gap in the workflow?** Open an issue describing the scenario.
- **Have a better phase structure?** Propose it with rationale.
- **Built a custom skill?** Submit a PR to `skills/`.
- **Improved a template?** We want to see it.

Read [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

This is not a finished product. It's an opinionated starting point designed to get better through real-world use. If you're building with AI and care about engineering discipline, your perspective is valuable.

---

## License

MIT — see [LICENSE](LICENSE).

---

<p align="center">
  <strong>AI writes code. This framework makes sure it's the right code.</strong>
</p>
