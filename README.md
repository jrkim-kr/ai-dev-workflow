# AI Dev Workflow

End-to-end project development workflow powered by Claude Code skills.

## Overview

`/instant-dev-flow` orchestrates 7 sequential phases + continuous commit to manage the full project lifecycle:

```
Phase 1       Phase 2       Phase 3       Phase 4       Phase 5       Phase 6       Phase 7
Intake     →  Discover   →  Plan       →  Implement  →  Review     →  Test       →  Deliver
─────────    ─────────     ─────────     ─────────     ─────────     ─────────     ─────────
Clarify       spec.md       Validate      Write code    Product +     Unit/Integ/   Setup guides
mvp-prd.md    system-       checklist.md  Frontend +    Code review   E2E tests     README.md
              design.md                   Backend                     Reports

                        ┌─────────────────────────────────────┐
                        │  commit (anytime): Error Doc / ADR  │
                        │  / Git Commit Convention            │
                        └─────────────────────────────────────┘
```

## Skills

### Core Orchestrator

| Skill | Description |
|-------|-------------|
| [instant-dev-flow](skills/instant-dev-flow/) | Main workflow orchestrator — 7 phases + continuous commit |

### Phase Skills

| Phase | Skill | Role |
|-------|-------|------|
| 1. Intake | [prd](skills/prd/) | PRD generation with structured discovery |
| 2. Discover | [project-structure](skills/project-structure/) | Project structure analysis |
| 2. Discover | [web-project-planner](skills/web-project-planner/) | spec.md, system-design.md, migrations, checklist.md |
| 4. Implementation | [issue-commit](skills/issue-commit/) | Error documentation + git commit workflow |
| 5. Review | [code-review-expert](skills/code-review-expert/) | SOLID, security, code quality review |
| 6. Testing | [test-verify](skills/test-verify/) | Test generation, execution, verification |
| 7. Deliver | [project-docs](skills/project-docs/) | Documentation sync & validation |

### References

The `instant-dev-flow` skill includes convention references:

| Reference | Purpose |
|-----------|---------|
| [branch-convention.md](skills/instant-dev-flow/references/branch-convention.md) | Git branch naming rules |
| [commit-convention.md](skills/instant-dev-flow/references/commit-convention.md) | Conventional Commits format |
| [pr-issue-template.md](skills/instant-dev-flow/references/pr-issue-template.md) | GitHub PR & issue templates |
| [docs-structure.md](skills/instant-dev-flow/references/docs-structure.md) | docs/ directory layout |
| [docs-naming-convention.md](skills/instant-dev-flow/references/docs-naming-convention.md) | File naming rules |
| [gitignore-templates.md](skills/instant-dev-flow/references/gitignore-templates.md) | .gitignore templates |
| [env-convention.md](skills/instant-dev-flow/references/env-convention.md) | Environment variables convention |
| [readme-template.md](skills/instant-dev-flow/references/readme-template.md) | README.md template |

## Docs Structure

Projects using this workflow generate documentation under `docs/`:

```
docs/
├── mvp-prd.md              ← Product Requirements Document
├── spec.md                 ← Feature Specification
├── system-design.md        ← System Design + ADRs
├── checklist.md            ← Unified Implementation Checklist
├── workflow-state.md       ← Workflow State (auto-generated)
├── schema/                 ← DB schema & migrations
├── test/                   ← Test reports
├── guides/                 ← Setup guides
└── errors/                 ← Error documentation
```

## Installation

Copy the skills you need into your `~/.claude/skills/` directory:

```bash
# Clone this repo
git clone https://github.com/jrkim-kr/ai-dev-workflow.git

# Copy all skills
cp -r ai-dev-workflow/skills/* ~/.claude/skills/
```

## Usage

```bash
# Start a new project
/instant-dev-flow intake

# Check workflow status
/instant-dev-flow status

# Continue to next phase
/instant-dev-flow discover
/instant-dev-flow plan
/instant-dev-flow impl
/instant-dev-flow review
/instant-dev-flow test
/instant-dev-flow deliver

# Commit anytime
/instant-dev-flow commit
```

## Changelog

### 2026-03-16

**instant-dev-flow** — 3 improvements based on real project usage:

1. **Terminology: "Phase" vs "Step"**
   - checklist.md now uses **"Step 0, Step 1, ..."** instead of "Phase 0, Phase 1, ..."
   - "Phase" is reserved exclusively for the 7 workflow phases (Phase 1 Intake ~ Phase 7 Deliver)
   - Prevents confusion between checklist implementation progress and workflow phase progress

2. **Phase 5 Review — Documentation Status Sync (new step)**
   - Added mandatory step to update status markers in all docs under `docs/`
   - Covers: mvp-prd.md, spec.md, system-design.md, checklist.md, schema/
   - Ensures docs reflect actual implementation state before code review

3. **Implementation boundary guard**
   - When all checklist steps are complete, `impl` now directs to Phase 5 Review instead of deployment
   - Prevents skipping the review/test cycle

## License

MIT
