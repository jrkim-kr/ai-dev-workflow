---
name: instant-dev-flow
description: "End-to-end project development workflow: Intake → Discover → Plan → Implement → Review → Test → Deliver. Manages 7 phases with continuous commit support. Integrates PRD generation, system design, frontend design skills, code review, testing, and delivery documentation."
argument-hint: "[intake | discover | plan | impl | review | test | deliver | commit | status]"
user-invocable: true
---

# Instant Dev Flow

## Purpose

Manage the full project lifecycle through **7 sequential phases + continuous commit**:

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

## State Tracking

Workflow state is recorded in `docs/workflow-state.md`. If this file doesn't exist, start from Phase 1.

### workflow-state.md Format

```markdown
# Workflow State

| Phase | Status | Completion Date |
|-------|--------|-----------------|
| 1. Intake | completed / in_progress / pending | YYYY-MM-DD |
| 2. Discover | ... | ... |
| 3. Plan | ... | ... |
| 4. Implementation | ... | ... |
| 5. Review | ... | ... |
| 6. Testing | ... | ... |
| 7. Deliver | ... | ... |

## Current Phase: N
## Current Step: (detailed step if Phase 4)
## Last Updated: YYYY-MM-DD
```

---

## Behavior by Argument

### No argument or `status`

1. Read `docs/workflow-state.md`
2. If file doesn't exist: treat as new project and guide to Phase 1
3. If file exists: summarize current phase status and next action

Output format:
```
## Workflow Status

| Phase | Status | Notes |
|-------|--------|-------|
| 1. Intake | ✅ Complete | 2026-03-15 |
| 2. Discover | 🔄 In Progress | Working on spec.md |
| 3~7 | ⬜ Pending | |

**Next action:** Run `/instant-dev-flow discover` to complete spec.md
```

---

### `intake` — Phase 1: Intake

**Purpose:** Clarify the project idea, generate clarification questions, and produce the MVP PRD.

#### Input Sources

1. **Notion Ideas DB (preferred):** If Notion MCP is available, read the specified row from the Ideas DB.
   - Ask user for the Notion page URL or row identifier
   - Extract: idea title, description, target users, notes
2. **Manual input (fallback):** If Notion MCP is unavailable, ask user to paste or describe the project idea.

> **Notion MCP extension point:** When Notion MCP tools become available, replace manual input with direct DB read. The expected Notion DB columns are: Title, Description, Target Users, Status, Priority, Notes.

#### Execution Steps

1. **Collect project idea** from Notion or manual input
2. **Quick Clarification** using AskUserQuestion (**1 round only**, max 5 questions):
   - What problem does this solve and who are the target users?
   - What are the must-have features for MVP? (and explicit non-goals)
   - Any preferred tech stack, existing infrastructure, or integrations?
   - Key user flows to support?
   - Timeline or deadline constraints?

   > Keep it to 1 round. Infer reasonable defaults for anything not answered.

3. **Generate `docs/mvp-prd.md` draft** following the structure below
4. **Present PRD draft to user** for review — user can request changes inline
   - Iterate on feedback until user confirms (this replaces multiple Q&A rounds)
5. **Create/update `docs/workflow-state.md`** (Phase 1 = completed)

#### mvp-prd.md Structure

> Reference: [awesome-copilot/prd](https://skills.sh/github/awesome-copilot/prd) + `/prd` skill

```markdown
# [Project Name] — MVP Product Requirements Document

> Last modified: [Date] | Version: v1.0

---

## 1. Executive Summary

### Problem Statement
[1-2 sentences on the pain point]

### Proposed Solution
[1-2 sentences on the approach]

### Success Criteria
- [Measurable KPI 1]
- [Measurable KPI 2]
- [Measurable KPI 3]

---

## 2. User Experience & Functionality

### User Personas
| Persona | Role | Goals | Pain Points |
|---------|------|-------|-------------|
| ... | ... | ... | ... |

### User Stories
- As a [persona], I want to [action] so that [benefit]

### Acceptance Criteria
- [ ] [Concrete, testable criterion]

### Non-Goals (Out of MVP Scope)
- [Feature explicitly excluded and why]

---

## 3. Functional Requirements

### Core Features (MVP)
| # | Feature | Description | Priority |
|---|---------|-------------|----------|
| F1 | ... | ... | Must-have |

### Feature Details
#### F1: [Feature Name]
- **Description:** ...
- **User Flow:** ...
- **Acceptance Criteria:** ...
- **Edge Cases:** ...

---

## 4. Non-Functional Requirements

| Category | Requirement |
|----------|-------------|
| Performance | ... |
| Security | ... |
| Accessibility | ... |
| Responsive | ... |
| SEO | ... |

---

## 5. Technical Constraints & Assumptions

### Constraints
- [Tech stack, infrastructure, or resource constraints]

### Assumptions
- [Assumptions made during scoping]

### Dependencies
- [External dependencies]

---

## 6. Risks & Mitigations

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| ... | High/Med/Low | High/Med/Low | ... |

---

## 7. Phased Rollout

| Phase | Scope | Timeline |
|-------|-------|----------|
| MVP | [Core features] | [Target] |
| v1.1 | [Enhancements] | [Target] |
| v2.0 | [Expansion] | [Target] |
```

#### Completion Criteria
- [ ] `docs/mvp-prd.md` exists with all sections filled
- [ ] User has confirmed the PRD content
- [ ] `docs/workflow-state.md` updated

---

### `discover` — Phase 2: Discover

**Purpose:** Deep-dive into the problem space and produce technical specification documents.

#### Input Sources

1. **`docs/mvp-prd.md`** (from Phase 1)
2. **Notion Ideas DB** (optional — additional context if available)
3. **Project structure analysis** (auto-scan existing codebase)

#### Prerequisites
- Phase 1 complete (mvp-prd.md exists)

#### Execution Steps

1. **Project Structure Analysis:** Run `/project-structure analyze`
   - Parallel scan of all files + configs (package.json, tsconfig.json, etc.)
   - Detect framework, conventions, tech stack, monorepo indicators
   - Use the analysis results to inform document generation

2. **Update mvp-prd.md** if new insights from discovery require scope adjustments
   - Present changes to user for confirmation before updating

3. **Generate `docs/spec.md` + `docs/system-design.md` together** (single pass):

   > Read mvp-prd.md + project structure analysis, then generate both documents in one step to avoid redundant analysis.

   **spec.md** — Feature Specification (follow `/web-project-planner` Document 1):
   - System Overview (name, purpose, user types, tech stack)
   - Core State Flow (ASCII diagram + state definitions)
   - Per-Page/Feature Detailed Specification (inputs, behaviors, UI guide, constraints)
   - Notification/Messaging System (if applicable)
   - External Integrations (if applicable)
   - Non-Functional Requirements
   - Page Map (URL structure)
   - Future Expansion Considerations

   **system-design.md** — System Architecture:

   ```markdown
   # [Project Name] — System Design Document

   > Last modified: [Date] | Version: v1.0

   ## 1. System Architecture Diagram
   [ASCII diagram of overall architecture]

   ## 2. Tech Stack & Selection Rationale

   | Area | Technology | Rationale |
   |------|-----------|-----------|
   | Framework | Next.js (default) | [Why] |
   | Deployment | Vercel (default) | [Why] |
   | Database | [Selection] | [Why] |
   | Styling | [Selection] | [Why] |
   | Auth | [Selection] | [Why] |
   | State Mgmt | [Selection] | [Why] |
   | Other | [Selection] | [Why] |

   ## 3. Data Pipeline
   [ASCII diagram of data flow]

   ## 4. Directory Structure
   [Based on project structure analysis]

   ## 5. Key Design Decisions (ADR)
   ### ADR-001: [Decision Title]
   - Context / Decision / Rationale / Consequences

   ## 6. Performance Strategy
   ## 7. Security Considerations
   ## 8. Deployment Architecture
   ```

4. **Generate DB schema** (if applicable):
   - Follow `/web-project-planner` Document 3 format
   - `docs/schema/migrations/` + `docs/schema/schema-overview.md`

5. **Update `docs/workflow-state.md`** (Phase 2 = completed)

#### Completion Criteria
- [ ] `docs/mvp-prd.md` updated (if needed)
- [ ] `docs/spec.md` exists
- [ ] `docs/system-design.md` exists with architecture diagram + tech rationale
- [ ] `docs/schema/` exists (if DB is used)

---

### `plan` — Phase 3: Plan

**Purpose:** Validate that spec.md and system-design.md fulfill all mvp-prd.md requirements, then generate a unified checklist.

#### Prerequisites
- Phase 2 complete (spec.md, system-design.md exist)

#### Execution Steps

1. **Cross-document validation:**
   - Read `docs/mvp-prd.md` → extract all requirements (functional + non-functional)
   - Read `docs/spec.md` → extract all specified features
   - Read `docs/system-design.md` → extract architectural decisions

   ```markdown
   ### Requirements Coverage Matrix

   | PRD Requirement | spec.md Coverage | system-design.md Coverage | Status |
   |-----------------|-----------------|--------------------------|--------|
   | F1: [Feature] | Section 3.1 | ADR-001 | ✅ Covered |
   | F2: [Feature] | ❌ Missing | - | ❌ Gap |
   | NFR: Performance | Section N+3 | Section 7 | ⚠️ Partial |
   ```

2. **If gaps found:**
   - Present gap analysis to user
   - Guide user to update spec.md / system-design.md
   - Re-validate after updates
   - Do NOT proceed to checklist generation until all requirements are covered

3. **Generate `docs/checklist.md`** (Unified Implementation Checklist):

   > This replaces both plan.md and checklist.md — a single document that contains step-by-step implementation tasks with checkboxes.
   >
   > **IMPORTANT — Naming convention:** checklist.md uses **"Step"** (Step 0, Step 1, ...) for implementation units. **"Phase"** is reserved exclusively for the 7 workflow phases (Phase 1 Intake ~ Phase 7 Deliver). Never use "Phase" inside checklist.md to avoid confusion with workflow phases.

   ```markdown
   # [Project Name] — Implementation Checklist

   > Auto-generated from mvp-prd.md + spec.md | Last modified: [Date]
   > Total N steps · Each step is an independently functional unit

   ## Usage
   - Implement each step in order; check items with `[x]` as completed
   - Each step has completion criteria that must be met before proceeding
   - Order within tasks: File creation → Feature operation → Data integrity

   ---

   ## Step 0: Project Initial Setup

   ### 0-1. Project Creation
   - [ ] #1 Framework initialization (`npx create-next-app@latest`)
   - [ ] #2 Folder structure setup per system-design.md
   - [ ] #3 Linter/formatter configuration (ESLint + Prettier)
   - [ ] #4 .gitignore setup (see references/gitignore-templates.md)
   - [ ] #5 Environment variables setup (see references/env-convention.md)

   ### 0-2. Backend/DB Connection
   - [ ] #6 DB project creation and connection
   - [ ] #7 `.env.local` + `.env.example` setup
   - [ ] #8 Client utility files

   ### 0-3. DB Migration Execution
   - [ ] #9 001_initial_schema.sql executed
   - [ ] #10 002_add_indexes.sql executed
   - [ ] #11 Seed data verified

   ### 0-4. Design System Foundation
   - [ ] #12 Run `/teach-impeccable` for design guidelines
   - [ ] #13 Theme setup (colors, typography, spacing)
   - [ ] #14 Common components (Button, Input, Layout, etc.)
   - [ ] #15 Font configuration

   **Completion Criteria:** Dev server runs, DB connected, design tokens set

   ---

   ## Step 1~N: [Feature Name]

   ### N-1. [Task Group Name]
   - [ ] #NN [Task description] — `src/path/to/file`
   - [ ] #NN+1 [Task description]
   - [ ] #NN+2 [Verification: specific behavior]

   **Completion Criteria:** [Specific, testable criteria]

   ---

   ## Final Step: Deployment & Wrap-up

   ### Deployment Configuration
   - [ ] #NN Vercel/hosting setup
   - [ ] #NN+1 Environment variables in production
   - [ ] #NN+2 Domain configuration

   ### QA Checklist
   - [ ] #NN+3 Production build successful (`npm run build`)
   - [ ] #NN+4 No TypeScript errors
   - [ ] #NN+5 No ESLint errors
   - [ ] #NN+6 All pages accessible
   - [ ] #NN+7 Responsive design verified

   **Completion Criteria:** Production-ready state

   ---

   ## Comprehensive Verification

   ### spec.md Compliance
   - [ ] All pages/features implemented
   - [ ] All state flows working
   - [ ] Non-functional requirements met

   ### mvp-prd.md Compliance
   - [ ] All acceptance criteria satisfied
   - [ ] All user stories completable
   ```

   **Checklist writing rules:**
   - Each step is an independently functional unit
   - Step 0 is always project initial setup
   - Core user features first, admin features next
   - External integrations after core features
   - Deployment/QA in final step
   - 3-5 check items per task group
   - Sequential numbering (#N) across all items
   - Include file paths where applicable
   - Completion criteria must be specific and testable
   - **Use "Step N" headings, never "Phase N"** — "Phase" is reserved for workflow phases only

4. **Update `docs/workflow-state.md`** (Phase 3 = completed)

#### Completion Criteria
- [ ] Requirements coverage matrix shows 100% coverage
- [ ] `docs/checklist.md` exists with all phases
- [ ] All checklist items have sequential numbering

---

### `impl` or `impl [step]` — Phase 4: Implementation

**Purpose:** Implement features step by step following checklist.md.

#### Prerequisites
- Phase 3 complete (checklist.md exists)

#### Execution Steps

1. Read `docs/checklist.md` → identify current step (first unchecked item)
2. Read `docs/spec.md` → get detailed spec for the feature
3. Read `docs/system-design.md` → get architecture context
4. Implement code according to the step
5. Check off completed items in checklist.md with `[x]`
6. Update `docs/workflow-state.md` (record current step)

#### Frontend Implementation — Streamlined Design Flow

Frontend design uses **3 stages** instead of per-component skill chains:

| Stage | When | Skills | Purpose |
|-------|------|--------|---------|
| 0 | Once per project (Phase 0) | `/teach-impeccable` | Establish persistent design guidelines |
| 1 | Each UI component/page | `/frontend-design` | Core design — distinctive, production-grade UI (already includes responsive + consistency) |
| 2 | Once, after ALL pages built | `/adapt` + `/optimize` | Responsive polish + performance pass across entire app |
| 3 | Once, before review | `/polish` | Final quality pass — alignment, spacing, details |

**Frontend implementation rules:**
- Run `/teach-impeccable` once during Phase 0 setup
- For each page/component: run `/frontend-design` only (it handles core design, layout, responsiveness)
- After ALL components are built: run `/adapt` then `/optimize` once across the whole app
- Run `/polish` once as the very last step before Phase 5 review
- Do NOT run `/normalize`, `/harden`, `/clarify`, `/onboard` separately — their concerns are handled within `/frontend-design` and `/polish`

#### Backend Implementation

- Follow spec.md API endpoint specifications
- Apply error handling patterns from `errors/` documentation
- Follow security considerations from system-design.md

#### Implementation Rules
- Implement step by step, report to user after each step completion
- If a specific step number is given as argument, implement only that step
- If no argument, proceed sequentially from the next incomplete step
- Document errors using `/instant-dev-flow commit` when they occur
- **When all checklist steps are complete (including Final Step: Deployment & QA), do NOT proceed to deploy directly.** Instead, transition to workflow Phase 5 (Review) by prompting user to run `/instant-dev-flow review`. The checklist's "Final Step: Deployment & QA" build/lint checks can be run during `impl`, but actual deployment happens AFTER Phase 5 Review and Phase 6 Test pass.

#### Step Completion Report Format
```
## Step [N] Complete

### Created/Modified Files
- `src/...` — description

### Checklist Progress
- [x] #N item description
- [x] #N+1 item description

### Next Step
Step [N+1]: [name] → `/instant-dev-flow impl [N+1]`
```

---

### `review` — Phase 5: Review

**Purpose:** Product review against PRD/spec + code quality review.

#### Prerequisites
- At least 1 step completed in Phase 4

#### Execution Steps

1. **Product Review** (workflow-specific, done inline — no skill delegation):
   - Compare `docs/mvp-prd.md` acceptance criteria against implementation
   - Compare `docs/spec.md` features against implementation

   ```markdown
   ### Product Review — PRD Compliance

   | Acceptance Criteria | Status | Notes |
   |---------------------|--------|-------|
   | [Criterion from PRD] | ✅ / ❌ / ⚠️ | ... |

   ### Product Review — spec.md Compliance

   | Feature | Status | Notes |
   |---------|--------|-------|
   | [Feature from spec] | ✅ / ❌ / ⚠️ | ... |
   ```

2. **Documentation Status Sync:** Update status markers in **all** docs under `docs/` to reflect actual implementation state.
   - `docs/mvp-prd.md` — Feature table Status column (❌→✅), Acceptance Criteria checkboxes, Traceability table
   - `docs/spec.md` — Feature section headers, Page Map status, API status, Data Model status
   - `docs/system-design.md` — Component architecture (❌ NEW → ✅), directory structure, data flow diagrams
   - `docs/checklist.md` — Verify all implemented items are checked `[x]`, unchecked items match actual remaining work
   - `docs/schema/` — schema-overview.md if DB models were added/changed
   - Any other docs with status markers or "미구현/NEW/TODO" labels
   - Do NOT ask user for permission — this is a mandatory review step, not optional.

3. **Code Review:** Run `/code-review-expert` skill (single pass)
   - Covers: SOLID, security, maintainability, P0~P3 classification
   - Also checks: accessibility basics, performance anti-patterns, responsive issues
   - Do NOT separately run `/critique` or `/audit` — their key checks are covered here

4. **Overall Assessment:**
   - **P0/P1 issues**: Guide to fix and re-review
   - **No P0/P1 issues + all features covered**: Update `workflow-state.md` (Phase 5 = completed)

---

### `test` — Phase 6: Testing

**Purpose:** Generate and run tests, produce verification reports.

#### Prerequisites
- Phase 5 complete (no P0/P1 issues)

#### Execution Steps

1. **Check test infrastructure:** Verify test framework is installed
2. Run `/test-verify all` skill — performs:
   - Test environment setup
   - Map checklist.md items to assertions
   - Unit + integration + E2E test generation
   - Test execution and verification report
3. **Check verdict:**
   - **PASS**: Update `workflow-state.md` (Phase 6 = completed)
   - **CONDITIONAL_PASS**: Share list with user, confirm whether to proceed
   - **FAIL**: Guide to fix and re-verify

#### Phase 6 Artifacts
- Test files: `*.test.ts`, `*.bench.ts`
- Test fixtures: `__fixtures__/` or `__testdata__/`
- `docs/test/test-report.md` — automated test results
- `docs/test/manual-test-case.md` — manual verification scenarios

---

### `deliver` — Phase 7: Deliver

**Purpose:** Generate delivery documentation for developers and end users.

#### Prerequisites
- Phase 6 complete (tests passed)

#### Execution Steps

1. **Generate `docs/guides/setup-guide-developer.md`:**

   ```markdown
   # [Project Name] — Developer Setup Guide

   ## Prerequisites
   - Node.js >= [version]
   - [Other prerequisites]

   ## Quick Start
   1. Clone the repository
   2. Install dependencies: `npm install`
   3. Set up environment variables (copy `.env.example` → `.env.local`)
   4. Run DB migrations (if applicable)
   5. Start dev server: `npm run dev`

   ## Environment Variables
   | Variable | Description | Required | Default |
   |----------|-------------|----------|---------|
   | ... | ... | Yes/No | ... |

   ## Project Structure
   [Key directories and their purposes]

   ## Development Workflow
   - Branch naming: see references/branch-convention.md
   - Commit convention: see references/commit-convention.md
   - PR process: see references/pr-issue-template.md

   ## Testing
   - Run tests: `npm test`
   - Run E2E: `npm run test:e2e`

   ## Deployment
   [Deployment instructions]

   ## Troubleshooting
   [Common issues and solutions — reference `errors/` directory]
   ```

2. **Generate `docs/guides/setup-guide-end-user.md`:**

   ```markdown
   # [Project Name] — User Guide

   ## Introduction
   [What the product does and who it's for]

   ## Getting Started
   [Step-by-step onboarding flow]

   ## Features
   ### [Feature 1]
   - How to use
   - Tips and best practices

   ## FAQ
   [Common questions and answers]

   ## Support
   [How to get help]
   ```

3. **Generate/update `README.md`** at project root:

   > Follow `references/readme-template.md` structure:

   ```markdown
   # [Project Name]

   ## Introduction
   [Brief description — what it does and why]

   ## Key Features
   - [Feature 1]
   - [Feature 2]
   - [Feature 3]

   ## Tech Stack
   | Area | Technology |
   |------|-----------|
   | Framework | ... |
   | Database | ... |
   | Deployment | ... |

   ## Project Structure
   [ASCII tree of key directories]

   ## Documentation
   | Document | Description |
   |----------|-------------|
   | [mvp-prd.md](docs/mvp-prd.md) | Product Requirements Document |
   | [spec.md](docs/spec.md) | Feature Specification |
   | [system-design.md](docs/system-design.md) | System Architecture + ADRs |
   | [checklist.md](docs/checklist.md) | Implementation Checklist |
   | [Developer Guide](docs/guides/setup-guide-developer.md) | Developer Setup |
   | [User Guide](docs/guides/setup-guide-end-user.md) | End User Guide |

   ## Quick Start
   [Minimal steps to get running]

   ## License
   [License info]
   ```

4. **Documentation sync & validation:** Run `/project-docs`
   - Verify all docs are in sync with current codebase
   - Fix broken links, terminology inconsistencies
   - Check completeness of all generated documents

5. **Update `docs/workflow-state.md`** (Phase 7 = completed)
6. **Workflow completion report:** overall phase dates, generated documents, next steps

#### Completion Criteria
- [ ] `docs/guides/setup-guide-developer.md` exists
- [ ] `docs/guides/setup-guide-end-user.md` exists
- [ ] `README.md` exists with all sections
- [ ] All workflow phases completed

---

### `commit` — Continuous Activity

**Purpose:** Error documentation, ADR records, and git commits. Can be run at any time during any phase.

#### Execution Steps

1. **Error Documentation** (when fixing bugs):
   - Create `errors/ERR-NNN-description.md` following `/issue-commit` Phase 1 format
   - Include: Summary, Root Cause, Reproduction Steps, Solution, Prevention Checklist

2. **ADR Documentation** (when making architectural decisions):
   - Append to `docs/system-design.md` ADR section:
   ```markdown
   ### ADR-NNN: [Decision Title]
   - **Date:** YYYY-MM-DD
   - **Context:** [Why this decision was needed]
   - **Decision:** [What was decided]
   - **Rationale:** [Why this option was chosen]
   - **Consequences:** [Trade-offs and implications]
   - **Status:** Accepted / Superseded by ADR-NNN
   ```

3. **Git Commit:** Delegate to `/issue-commit` skill
   - Follows commit convention (see `references/commit-convention.md`)
   - Follows branch convention (see `references/branch-convention.md`)

---

## Common Rules

1. **Phase skipping is prohibited**: Cannot proceed to next phase if previous phase is incomplete. Guide user to complete current phase.
2. **State file must be updated**: Always update `workflow-state.md` when each phase starts/completes.
3. **Follow related skill conventions**: Each phase follows the format and rules of its associated skill.
4. **Incremental progress**: Do not run all phases at once. **Confirm with user only after Phase 1 (Intake) and Phase 3 (Plan)** — these are decision checkpoints. Other phases auto-proceed unless errors occur.
5. **git pull first**: Sync latest code with `git pull` before starting any phase.
6. **Apply project conventions**: All generated files follow the conventions defined in `references/`.
7. **Terminology: "Phase" vs "Step"**: "Phase" (Phase 1~7) refers exclusively to the 7 workflow phases (Intake→Deliver). "Step" (Step 0, Step 1, ...) refers to implementation units inside `checklist.md`. Never mix these terms. This prevents confusion between checklist progress and workflow progress.

## Related Skill Mapping

| Phase | Delegated Skill | Role |
|-------|----------------|------|
| 1. Intake | `prd` | **Reference**: PRD structure |
| 2. Discover | `project-structure` | **Delegate**: Project structure analysis |
| 2. Discover | `web-project-planner` | **Reference**: spec/system-design generation |
| 3. Plan | — | **Self**: Cross-document validation + checklist generation |
| 4. Implementation (Frontend) | `teach-impeccable` (once), `frontend-design` (per page), `adapt` + `optimize` (once), `polish` (once) | **Delegate**: Streamlined 3-stage design flow |
| 4. Implementation (Backend) | `dom-selector` | **Reference**: DOM selector rules |
| 4. Implementation (Errors) | `issue-commit` (Phase 1) | **Reference**: Error documentation |
| 5. Review | `code-review-expert` | **Delegate**: Unified code + quality review |
| 6. Testing | `test-verify` | **Delegate**: Test generation/execution/verification |
| 7. Deliver | `project-docs` | **Delegate**: Documentation sync & validation |
| 7. Deliver | — | **Self**: Guide/README generation |
| Commit (continuous) | `issue-commit` | **Delegate**: git commit workflow |

## Docs Directory Structure

```
docs/
├── mvp-prd.md                        ← Product Requirements Document
├── spec.md                           ← Feature Specification
├── system-design.md                  ← System Design + ADRs
├── checklist.md                      ← Unified Implementation Checklist
├── workflow-state.md                 ← Workflow State (auto-generated)
├── schema/
│   ├── schema-overview.md            ← Schema Overview
│   └── migrations/
│       ├── 001_initial_schema.sql
│       └── README.md
├── test/
│   ├── test-report.md                ← Automated Test Results
│   └── manual-test-case.md           ← Manual Test Scenarios
├── guides/
│   ├── setup-guide-developer.md      ← Developer Setup Guide
│   └── setup-guide-end-user.md       ← End User Guide
└── errors/
    └── ERR-NNN-description.md        ← Error Documentation
```
