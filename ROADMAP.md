# Roadmap

This document outlines the evolution plan for `ai-dev-workflow` from its initial release through a stable v1.0.

## Versioning Philosophy

Each version adds capability without breaking existing workflows. The 6-phase sequential model is the foundation — all evolution builds on top of it.

---

## v0.1 — Foundation (Current)

**Goal:** Establish the core workflow and prove the concept.

- [x] 6-phase sequential workflow (Plan → Design → Implement → Review → Test → Commit)
- [x] Core skill definitions (`dev-workflow`, `log-error`, `issue-commit`, `test-verify`)
- [x] Document templates (`spec.md`, `plan.md`, `checklist.md`, `workflow-state.md`)
- [x] State tracking via `workflow-state.md`
- [x] Framework documentation (philosophy, architecture, lifecycle)
- [x] Example project (reservation system)
- [ ] Community feedback collection

---

## v0.2 — Configurable Rules

**Goal:** Allow teams to customize phase behavior without forking.

- [ ] `.dev-workflow.yaml` configuration file
- [ ] Configurable phase entry/exit criteria
- [ ] Optional phases (e.g., skip design for hotfixes with explicit override)
- [ ] Custom validation hooks per phase
- [ ] Phase timeout warnings
- [ ] Configuration schema validation

---

## v0.3 — Multi-Agent Support

**Goal:** Support workflows where multiple AI agents collaborate.

- [ ] Agent role definitions (architect, implementer, reviewer, tester)
- [ ] Agent handoff protocols between phases
- [ ] Shared state management across agents
- [ ] Conflict resolution for concurrent modifications
- [ ] Agent-specific skill variants

---

## v0.5 — Plugin System

**Goal:** Enable community extensibility without core modifications.

- [ ] Plugin API specification
- [ ] Custom skill registration mechanism
- [ ] Community skill registry
- [ ] Skill composition (combine multiple skills into custom phases)
- [ ] Plugin dependency management
- [ ] Skill testing framework

---

## v0.7 — CI/CD Integration

**Goal:** Enforce workflow discipline in automated pipelines.

- [ ] GitHub Actions workflow templates
- [ ] Pre-commit hooks for phase validation
- [ ] PR template generation from workflow state
- [ ] Automated spec-to-test coverage reporting
- [ ] Pipeline stage gates tied to workflow phases
- [ ] Integration with popular CI platforms (GitHub Actions, GitLab CI, CircleCI)

---

## v1.0 — Stable Release

**Goal:** Production-ready framework with stable API and comprehensive ecosystem.

- [ ] Stable configuration API (no breaking changes post-1.0)
- [ ] Comprehensive documentation with tutorials
- [ ] Migration guides from ad-hoc AI workflows
- [ ] Performance benchmarks (workflow overhead measurement)
- [ ] Enterprise patterns (multi-repo, monorepo, team workflows)
- [ ] Certification program for community skills
- [ ] Case studies from production deployments

---

## Beyond v1.0

Exploration areas under consideration:

- **Workflow analytics** — Metrics on phase duration, review iteration count, test coverage trends
- **AI model abstraction** — Support for non-Claude AI assistants (GPT, Gemini, Codex)
- **Visual workflow editor** — GUI for customizing phase sequences and rules
- **Team collaboration** — Multi-developer workflow coordination
- **Regulatory compliance** — Audit trail generation for regulated industries

---

## Contributing to the Roadmap

The roadmap is not fixed. If you have use cases that aren't addressed, or you think the priority order should change, open an issue tagged `[roadmap]`.

We prioritize based on:
1. **Real-world pain points** — Problems people actually encounter
2. **Foundation stability** — Core workflow reliability over feature breadth
3. **Community demand** — Features requested by multiple independent users
