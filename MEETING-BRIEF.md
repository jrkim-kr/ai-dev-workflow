# AI Dev TF — Skill Review Brief

> Prepared by: JR Kim (via Fathan)
> Date: 2026-03-17
> Repo: https://github.com/jrkim-kr/ai-dev-workflow

---

## What I Built

**`/instant-dev-flow`** — An end-to-end project development workflow skill for Claude Code that orchestrates 7 phases with 18 integrated sub-skills.

```
Intake → Discover → Plan → Implement → Review → Test → Deliver
  + continuous commit (anytime)
```

---

## How It Maps to Our Phase 0 Goals

### 1. Workflow Agent Skill → `/instant-dev-flow`

This IS a workflow agent skill. Here's what it does:

| Phase | What Happens | Output |
|-------|-------------|--------|
| 1. Intake | Clarification Q&A → PRD generation | `docs/mvp-prd.md` |
| 2. Discover | Codebase analysis → spec + architecture | `docs/spec.md`, `docs/system-design.md` |
| 3. Plan | Cross-doc validation → implementation checklist | `docs/checklist.md` |
| 4. Implement | Step-by-step coding with design skill chain | Working code |
| 5. Review | Product review + UX critique + code review | Review report |
| 6. Test | Unit/integration/E2E test generation & execution | `docs/test/test-report.md` |
| 7. Deliver | Setup guides + README + doc sync | `docs/guides/`, `README.md` |
| Commit | Error docs + ADR + git commit (anytime) | `errors/`, commits |

**Key features:**
- Phase skipping prohibited — enforces sequential completion
- `workflow-state.md` tracks progress across sessions
- Each phase delegates to specialized sub-skills (not monolithic)
- Includes 8 reference files for team conventions (branch naming, commit format, PR templates, etc.)

### 2. claude.md Checker → `/project-docs` (Phase 4)

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
├── prd                     ← PRD generation
├── project-structure       ← Codebase analysis
├── web-project-planner     ← spec/design/checklist generation
├── frontend-design chain   ← 9 design skills (teach-impeccable → polish)
├── code-review-expert      ← SOLID/security review
├── critique                ← UX design evaluation
├── audit                   ← a11y/performance audit
├── test-verify             ← Test generation/execution
├── project-docs            ← Doc sync & validation
├── issue-commit            ← Git commit workflow
└── references/             ← 8 convention files
```

All skills are **independently usable** — you don't need the full workflow to use `/prd` or `/code-review-expert` alone.

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

4. **Next steps**
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
