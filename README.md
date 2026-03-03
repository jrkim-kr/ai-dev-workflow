# ai-dev-workflow

**AI writes code. It doesn't enforce discipline. This workflow does.**

An opinionated, spec-driven development framework that enforces process discipline when building with AI coding assistants. Six sequential phases. No skipping. No shortcuts.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

---

## The Problem

AI coding assistants generate code fast. But speed without structure creates chaos:

- **No spec verification.** AI codes before requirements are validated.
- **No deterministic progression.** Tasks jump between phases randomly. State is lost.
- **No enforced review.** Generated code ships without systematic quality checks.
- **No test mapping.** Tests are disconnected from requirements.
- **No audit trail.** No record of what was planned vs. what was built.

---

## Philosophy

| Principle | Meaning |
|-----------|---------|
| **Spec-Driven** | Every project starts with a structured specification. AI cannot satisfy requirements that don't exist. |
| **State-Tracked** | Explicit state in `workflow-state.md`. You always know which phase you're in. |
| **Sequential** | Plan → Design → Implement → Review → Test → Commit. No skipping. |
| **Review-Mandatory** | Every implementation is reviewed against the spec. P0–P3 severity ratings. |
| **Test-Mapped** | Every functional requirement maps to at least one test case. |

---

## Architecture

`/dev-workflow` is an **orchestrator** — it validates prerequisites, delegates to specialized skills, and updates state. It does not perform work directly (except implementation).

```
/dev-workflow plan    → delegates to → /web-project-planner plan
/dev-workflow design  → delegates to → /web-project-planner design
/dev-workflow impl    → executes directly (step-by-step)
/dev-workflow review  → delegates to → /code-review-expert + spec compliance
/dev-workflow test    → delegates to → /test-verify all
/dev-workflow commit  → delegates to → /issue-commit
```

Each skill is also **independently usable** outside the workflow for existing project maintenance.

---

## The Six Phases

| Phase | Command | Delegates To | Output |
|-------|---------|-------------|--------|
| 1. Plan | `/dev-workflow plan` | `web-project-planner` | spec.md, system-design.md, migrations/ |
| 2. Design | `/dev-workflow design` | `web-project-planner` | plan.md, checklist.md |
| 3. Implement | `/dev-workflow impl` | — (self) | Source code |
| 4. Review | `/dev-workflow review` | `code-review-expert` | Review report (P0–P3) |
| 5. Test | `/dev-workflow test` | `test-verify` | Tests, verification report |
| 6. Commit | `/dev-workflow commit` | `issue-commit` | GitHub issues, commits, push |

State tracked in `docs/plan/workflow-state.md`. Previous phase must be complete before the next can begin.

---

## Quick Start

### Install skills

```bash
cp skills/*.md ~/.claude/skills/
```

### New project (step-by-step)

```bash
/dev-workflow plan      # Phase 1: Q&A → spec, system design, migrations
/dev-workflow design    # Phase 2: implementation plan + checklist
/dev-workflow impl      # Phase 3: code step-by-step
/dev-workflow review    # Phase 4: spec compliance + code quality (P0–P3)
/dev-workflow test      # Phase 5: generate & run tests, verification report
/dev-workflow commit    # Phase 6: GitHub issues + commit + push
```

### Existing project (standalone skills)

```bash
/code-review-expert     # Review current git changes
/test-verify verify     # Run existing tests + verification report
/issue-commit           # Create issues + commit + push
```

---

## Project Structure

```
ai-dev-workflow/
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── ROADMAP.md
│
├── skills/                        # Skill definitions
│   ├── dev-workflow.md            # Master orchestrator
│   ├── web-project-planner.md    # Phase 1+2: planning & design
│   ├── code-review-expert.md     # Phase 4: code review (P0–P3)
│   ├── test-verify.md            # Phase 5: test generation & verification
│   ├── issue-commit.md           # Phase 6: issues + commit + push
│   └── log-error.md              # Cross-cutting: error documentation
│
├── templates/                     # Document templates
│   ├── spec.md, plan.md, checklist.md, workflow-state.md
│
├── docs/
│   └── architecture.md           # System architecture & design decisions
│
└── examples/
    └── example-reservation-system/  # Complete worked example
```

---

## Roadmap

| Version | Focus | Status |
|---------|-------|--------|
| **v0.1** | Core 6-phase workflow, skill definitions, templates | **Current** |
| **v0.2** | Configurable phase rules, custom validation hooks | Planned |
| **v0.3** | Multi-agent workflow support | Planned |
| **v0.5** | Plugin system, community skill registry | Planned |
| **v1.0** | Stable API, ecosystem maturity | Planned |

See [ROADMAP.md](ROADMAP.md) for details.

---

## Contributing

- **Found a gap?** Open an issue.
- **Better phase structure?** Propose with rationale.
- **Built a custom skill?** Submit a PR.

Read [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## Acknowledgments

- The `code-review-expert` skill was inspired by [sanyuan0704/sanyuan-skills](https://github.com/sanyuan0704/sanyuan-skills) for structured, multi-dimensional code review.

---

## License

MIT — see [LICENSE](LICENSE).

---

<p align="center">
  <strong>AI writes code. This framework makes sure it's the right code.</strong>
</p>
