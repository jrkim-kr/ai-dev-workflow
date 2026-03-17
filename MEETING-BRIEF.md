# AI Dev TF — Skill Review Brief

> Prepared by: JR Kim (via Fathan)
> Date: 2026-03-17 (Updated)
> Repo: https://github.com/jrkim-kr/ai-dev-workflow

---

## What I Built

**`/instant-dev-flow`** — An end-to-end project development workflow skill for Claude Code that orchestrates 7 phases with continuous commit support.

```
Phase 1       Phase 2       Phase 3       Phase 4       Phase 5       Phase 6       Phase 7
Intake     →  Discover   →  Plan       →  Implement  →  Review     →  Test       →  Deliver

                        ┌─────────────────────────────────────┐
                        │  commit (anytime): Error Doc / ADR  │
                        │  / Git Commit Convention            │
                        └─────────────────────────────────────┘
```

---

## How It Maps to Our Phase 0 Goals

### 1. Workflow Agent Skill → `/instant-dev-flow`

This IS a workflow agent skill. Here's what it does:

| Phase | What Happens | Output |
|-------|-------------|--------|
| 1. Intake | Clarification Q&A (1 round) → PRD generation | `docs/mvp-prd.md` |
| 2. Discover | Codebase analysis → spec + architecture + DB schema | `docs/spec.md`, `docs/system-design.md`, `docs/schema/` |
| 3. Plan | Cross-doc validation → implementation checklist | `docs/checklist.md` |
| 4. Implement | Step-by-step coding with streamlined design flow | Working code |
| 5. Review | Product review + doc status sync + code review | Review report, updated docs |
| 6. Test | Unit/integration/E2E test generation & execution | `docs/test/test-report.md` |
| 7. Deliver | Setup guides + README + doc sync | `docs/guides/`, `README.md` |
| Commit | Error docs + ADR + git commit (anytime) | `errors/`, commits |

**Key features:**
- Phase skipping prohibited — enforces sequential completion
- `workflow-state.md` tracks progress across sessions
- Each phase delegates to specialized sub-skills (not monolithic)
- Includes 8 reference files for team conventions (branch naming, commit format, PR templates, etc.)
- **"Phase" vs "Step" terminology** — "Phase" (1~7) = workflow phases only; "Step" (0, 1, ...) = implementation units in checklist.md. Prevents confusion between workflow progress and implementation progress.

**Recent improvements (2026-03-16):**
1. **Terminology fix**: checklist.md uses "Step 0, Step 1, ..." instead of "Phase 0, Phase 1, ..."
2. **Phase 5 doc sync**: Mandatory documentation status sync before code review — updates status markers across all docs under `docs/`
3. **Implementation boundary guard**: When all checklist steps are complete, `impl` directs to Phase 5 Review instead of deployment — prevents skipping review/test cycle

### 2. claude.md Checker → `/project-docs` (Phase 7)

The `/project-docs` skill already has document validation logic:

| Check | What It Does |
|-------|-------------|
| Broken Links | Scans `[text](path)` references, verifies targets exist |
| Terminology | Extracts key terms across docs, checks consistency with code |
| Cross-References | Detects missing links between related docs |
| Duplicates | Finds same content in multiple files, suggests consolidation |
| Completeness | Verifies required sections exist per document type |

**To make a claude.md-specific checker**, we could extract and specialize this logic to validate:
- Required CLAUDE.md sections (project description, conventions, etc.)
- Consistency between CLAUDE.md and actual project structure
- Outdated references in CLAUDE.md

---

## Skill Architecture

```
instant-dev-flow (orchestrator)
├── prd                     ← PRD generation (Phase 1 reference)
├── project-structure       ← Codebase analysis (Phase 2 delegate)
├── web-project-planner     ← spec/design/checklist generation (Phase 2 reference)
├── frontend-design flow    ← Streamlined 3-stage design (Phase 4)
│   ├── teach-impeccable    ← Once per project (design guidelines)
│   ├── frontend-design     ← Per page/component (core design)
│   ├── adapt + optimize    ← Once after all pages built
│   └── polish              ← Once before review (final pass)
├── code-review-expert      ← Unified code + quality review (Phase 5)
├── test-verify             ← Test generation/execution (Phase 6)
├── project-docs            ← Doc sync & validation (Phase 7)
├── issue-commit            ← Git commit workflow (continuous)
├── dom-selector            ← DOM selector rules (Phase 4 reference)
└── references/             ← 8 convention files
```

**Design flow simplification:** Previously called 9 separate design skills per component. Now uses a **streamlined 3-stage approach** — `/frontend-design` handles core design per page, then `/adapt` + `/optimize` + `/polish` run once across the whole app. Skills like `/normalize`, `/harden`, `/clarify`, `/onboard` are no longer called separately (their concerns are covered by `/frontend-design` and `/polish`).

All skills are **independently usable** — you don't need the full workflow to use `/prd` or `/code-review-expert` alone.

---

## Phase 5 Review — New Doc Sync Step

This is a notable addition. Before running code review, Phase 5 now **mandatorily updates status markers** in all docs:

| Document | What Gets Updated |
|----------|-------------------|
| `mvp-prd.md` | Feature table Status column (❌→✅), Acceptance Criteria checkboxes |
| `spec.md` | Feature section headers, Page Map status, API status |
| `system-design.md` | Component architecture (❌ NEW → ✅), directory structure |
| `checklist.md` | Verify all implemented items are checked `[x]` |
| `schema/` | schema-overview.md if DB models changed |

This ensures docs reflect actual implementation state before code review happens.

---

## Discussion Points for the Meeting

1. **Can this be our base Workflow Agent Skill?**
   - It's designed for web projects (Next.js default) — does it need to be more generic?
   - Should we add/remove phases for our team's workflow?

2. **claude.md Checker scope**
   - Start from `/project-docs` validation logic, or build from scratch?
   - What sections should be required in CLAUDE.md? (Let's define the standard)

3. **Conventions to adopt as team standard**
   - The `references/` folder has ready-to-use conventions
   - Which ones should be mandatory vs. recommended?

4. **Design flow trade-offs**
   - Streamlined 3-stage vs. full 9-skill chain — is there a use case for the full chain?
   - Should `/critique` and `/audit` be optional add-ons for specific projects?

5. **Next steps**
   - Review the repo: https://github.com/jrkim-kr/ai-dev-workflow
   - Try running `/instant-dev-flow intake` on a sample project
   - Decide what to keep, modify, or discard before merging

---

## Quick Demo (for Fathan)

To show the skill in action:

```bash
# 1. Copy skills to local
cp -r ai-dev-workflow/skills/* ~/.claude/skills/

# 2. Start a new project workflow
/instant-dev-flow status    # Shows current state
/instant-dev-flow intake    # Starts Phase 1 with Q&A
```

---

> **Note from JR:** I couldn't attend the meeting, but I'm available for questions async. Feel free to reach out on Slack after the review.
