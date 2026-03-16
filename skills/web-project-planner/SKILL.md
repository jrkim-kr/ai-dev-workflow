---
name: web-project-planner
description: "Before building a new website, analyze the project structure and ask about requirements and tech stack to generate a feature spec (spec.md), system design doc (system-design.md), DB migrations (migrations/), and unified implementation checklist (checklist.md). Corresponds to Phase 2 (Discover) + Phase 3 (Plan) of /instant-dev-flow."
argument-hint: "[discover | plan | project-name (optional)]"
---

# Web Project Planner Skill

## Purpose

Before starting a new website project, analyze the project structure and collect requirements through interactive Q&A with the user to generate a **feature specification**, **system design document**, **DB migrations**, and **unified implementation checklist**.

> **Note:** This skill corresponds to Phase 2 (Discover) + Phase 3 (Plan) of `/instant-dev-flow`.

## Execution Modes

| Argument | Mode | Scope | Generated Documents |
|----------|------|-------|-------------------|
| (none) | Full | Phase 0 → Phase 1 → Phase 2 complete | spec.md, system-design.md, migrations, checklist.md |
| `discover` | Discover | Phase 0 → Phase 1 → Phase 2 (docs 1-3 only) | spec.md, system-design.md, migrations |
| `plan` | Plan | Read existing spec.md → Phase 2 (doc 4 only) | checklist.md |
| `[project-name]` | Full | Same as (none), uses project name in document titles | All |

> `plan` mode requires `docs/spec.md` and `docs/system-design.md` to already exist. If missing, guide user to run `discover` mode first.

## Execution Steps

### Phase 0: Project Structure Analysis (Automatic)

Delegate to `/project-structure analyze` for thorough project analysis.

1. **Run `/project-structure analyze`** which performs:
   - Parallel scan of all files + configs (package.json, tsconfig.json, etc.)
   - Framework detection (Next.js, React, Vue, etc.)
   - Monorepo indicator check (workspaces, turbo.json, nx.json)
   - Directory structure evaluation and issue identification

2. **Use analysis results to inform document generation:**
   - Reflect actual structure in system-design.md directory structure section
   - Reflect current project state in checklist.md Phase 0
   - If existing documents are found, confirm with user before overwriting

---

### Phase 1: Requirements Gathering (Interactive Q&A)

> **Q&A templates are defined in `references/questions.md`.** Read that file for all question definitions (Round 1~3 + follow-up rules).

**Summary:** 3 rounds of AskUserQuestion (up to 4 questions each), plus follow-up as needed:
- **Round 1:** Project type, user types, core feature, project name
- **Round 2:** Frontend framework, backend/DB, deployment, UI styling (use Phase 0 detection as defaults)
- **Round 3:** Notifications, external integrations, design concept, additional requirements (dynamic based on Round 1)

---

### Phase 2: Document Generation

Generate documents under the `docs/` directory based on collected requirements.

- **`discover` mode**: Generate docs 1-3 only (spec.md, system-design.md, migrations)
- **`plan` mode**: Generate doc 4 only (checklist.md) — skip Phases 0-1 and read existing spec.md
- **No argument / project name**: Generate all docs 1-4

#### Document 1: `docs/spec.md` (Feature Specification) — included in `discover` mode

Follow this structure:

```markdown
# [Project Name] — Feature Specification

> Last modified: [Date]
> Version: v1.0

---

## 1. System Overview

| Item | Details |
|------|---------|
| System Name | [Project Name] |
| Purpose | [System purpose and problem it solves] |
| User Types | [List of user roles] |
| Tech Stack | [Selected tech stack] |

---

## 2. Core State Flow

[Express the system's core state flow as an ASCII diagram]

### State Definitions

| State | Code | Description | Trigger |
|-------|------|-------------|---------|
| ... | ... | ... | ... |

---

## 3~N. Per-Page/Feature Detailed Specification

[For each page/feature, include]:
- Page purpose/nature
- Input field table (field, type, required, description)
- Post-submission behavior
- UI guide (tone, colors, layout direction)
- Constraints

---

## N+1. Automated Notification/Messaging System (if applicable)

### Sending Conditions Table
### Message Templates (actual sending format)

---

## N+2. External Integrations (if applicable)

[For each external service]:
| Item | Details |
|------|---------|
| Trigger | ... |
| Authentication | ... |
| Processing | ... |

---

## N+3. Non-Functional Requirements

| Item | Details |
|------|---------|
| Responsive | ... |
| Accessibility | ... |
| Performance | ... |
| Security | ... |
| SEO | ... |
| Deployment | ... |

---

## N+4. Page Map

[Full URL structure as ASCII tree]

---

## N+5. Future Expansion Considerations (Post-MVP)

[Features excluded from MVP but planned for future expansion]
```

#### Document 2: `docs/system-design.md` (System Design Document) — included in `discover` mode

Write based on Phase 0 project structure analysis results:

```markdown
# [Project Name] — System Design Document

> Last modified: [Date] | Version: v1.0

## 1. Design Principles

- List core design principles (e.g., client-side processing priority, serverless, etc.)

## 2. Tech Stack

| Area | Technology | Rationale |
|------|-----------|-----------|
| Framework | ... | ... |
| Styling | ... | ... |
| State Management | ... | ... |
| DB | ... | ... |
| Deployment | ... | ... |

## 3. Architecture Overview

[Visualize overall system architecture with ASCII diagram]

## 4. Data Flow

[Express major data flows with ASCII diagram]

## 5. Directory Structure

[Write based on actual project structure analyzed in Phase 0]
[Explain each directory's role and organizational principles]

## 6. Key Design Decisions (ADR)

### ADR-001: [Decision Title]
- Context: ...
- Decision: ...
- Rationale: ...
- Consequences: ...

## 7. Performance Strategy

- Rendering strategy, caching, bundle optimization, etc.

## 8. Security Considerations

- Auth/authorization, input validation, CORS, etc.
```

#### Document 3: DB Migrations (migration structure) — included in `discover` mode

Generate as a **migration directory structure** instead of a single schema.sql.

**Directory structure:**
```
docs/schema/
├── migrations/
│   ├── 001_initial_schema.sql        # Initial table creation
│   ├── 002_add_indexes.sql           # Add indexes
│   ├── 003_add_rls_policies.sql      # RLS policies (Supabase)
│   ├── 004_functions_triggers.sql    # Functions, triggers
│   ├── 005_seed_data.sql             # Seed data
│   └── README.md                     # Migration execution guide
└── schema-overview.md                # Schema overview (ERD description)
```

**Migration file format:**

Each migration file follows this format:

```sql
-- ============================================================
-- Migration: NNN_description
-- Created: YYYY-MM-DD
-- Description: [Purpose of this migration]
-- ============================================================

-- UP (Apply)
-- ============================================================

[DDL/DML statements]

-- DOWN (Rollback)
-- ============================================================

[Rollback DDL/DML statements]
```

**Migration separation criteria:**
- `001_initial_schema.sql` — Extensions, ENUM types, core table creation
- `002_add_indexes.sql` — Indexes, foreign key constraints
- `003_add_rls_policies.sql` — Row Level Security policies (when using Supabase)
- `004_functions_triggers.sql` — Functions, triggers (e.g., auto-update updated_at)
- `005_seed_data.sql` — Initial data insertion
- Feature additions: `006_add_[feature]_tables.sql`

**Schema writing rules:**
- For Supabase: Use `uuid-ossp` extension, uuid PK, timestamptz
- For Firebase/NoSQL: Define collection/document structure as comments and create Firestore security rules in a separate file (`docs/schema/firestore.rules`)
- For dedicated backend servers: May create ORM schema (Prisma etc.) as separate files
- All tables include `created_at`, `updated_at`
- Specify `on delete` policy for foreign keys
- Add indexes for frequently queried columns
- Comments required in each migration
- Every migration must include **rollback (DOWN)**

**schema-overview.md format:**

```markdown
# [Project Name] — Schema Overview

> Last modified: [Date]

## Table List

| Table | Purpose | Key Relationships |
|-------|---------|-------------------|
| users | User information | → reservations (1:N) |
| ... | ... | ... |

## ERD (ASCII)

[Visualize table relationships with ASCII diagram]

## Migration History

| Number | File | Description | Date |
|--------|------|-------------|------|
| 001 | initial_schema.sql | Initial table creation | YYYY-MM-DD |
| ... | ... | ... | ... |

## Migration Execution Guide

[Execution guide appropriate for the DB environment]
```

#### Document 4: `docs/checklist.md` (Unified Implementation Checklist) — included in `plan` mode

> In `plan` mode: Read existing `docs/spec.md` and `docs/system-design.md` to generate this document.
> This document replaces both plan.md and checklist.md — a single document containing phase-by-phase implementation tasks with checkboxes.

```markdown
# [Project Name] — Implementation Checklist

> Auto-generated from spec.md + system-design.md | Last modified: [Date]
> Total N phases · Each phase is an independently functional unit

## Usage
- Implement each phase in order; check items with `[x]` as completed
- Each phase has completion criteria that must be met before proceeding
- Order within steps: File creation → Feature operation → Data integrity

---

## Phase 0: Project Initial Setup

### 0-1. Project Creation
- [ ] #1 Framework initialization
- [ ] #2 Folder structure setup per system-design.md
- [ ] #3 Linter/formatter configuration
- [ ] #4 .gitignore setup
- [ ] #5 Environment variables setup

### 0-2. Backend/DB Connection
- [ ] #6 DB project creation and connection
- [ ] #7 `.env.local` + `.env.example` setup
- [ ] #8 Client utility files

### 0-3. DB Migration Execution
- [ ] #9 001_initial_schema.sql executed
- [ ] #10 002_add_indexes.sql executed
- [ ] #11 Seed data verified

### 0-4. Design System Foundation
- [ ] #12 Theme setup (colors, typography, spacing)
- [ ] #13 Common components (Button, Input, Layout, etc.)
- [ ] #14 Font configuration

**Completion Criteria:** Dev server runs, DB connected, design tokens set

---

## Phase 1~N: [Feature Name]

### N-1. [Step Name]
- [ ] #NN [Task description] — `src/path/to/file`
- [ ] #NN+1 [Task description]
- [ ] #NN+2 [Verification: specific behavior]

**Completion Criteria:** [Specific, testable criteria]

---

## Final Phase: Deployment & Wrap-up

### Deployment Configuration
- [ ] #NN Hosting setup
- [ ] #NN+1 Environment variables in production
- [ ] #NN+2 Domain configuration

### QA Checklist
- [ ] #NN+3 Production build successful
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

### Performance
- [ ] Initial load time
- [ ] Large data handling

### Build
- [ ] Production build successful
- [ ] No type errors
- [ ] No lint errors
```

**Checklist writing rules:**
- Each phase is an independently functional unit
- Phase 0 is always project initial setup
- Core user features first, admin features next
- External integrations after core features are complete
- Deployment/QA in the final phase
- 3-5 check items per step
- Sequential numbering (#N) for all items
- Completion criteria must be specific and verifiable

---

## Writing Rules (Common)

1. **Use clear language**: Write all documents in English.
2. **Use tables extensively**: Use markdown tables for listing items.
3. **ASCII diagrams**: Visualize state flows, page maps, and implementation order with ASCII diagrams.
4. **Cross-document consistency**: Terminology, table names, status codes, and page paths must be consistent across all documents.
5. **Practicality**: Write content at a level that can be directly used for implementation.
6. **Message templates**: If notification/messaging features exist, include actual message content as templates.
7. **Filenames**: lowercase-kebab-case.md.

## File Paths

```
docs/
├── spec.md                       ← Feature specification
├── system-design.md              ← System design document
├── checklist.md                  ← Unified implementation checklist
├── workflow-state.md             ← Workflow state (auto-generated)
└── schema/
    ├── schema-overview.md        ← Schema overview
    └── migrations/
        ├── 001_initial_schema.sql
        ├── 002_add_indexes.sql
        ├── ...
        └── README.md             ← Migration guide
```

- If a project name is given as argument, use it in document titles
- Create directories if they don't exist

## After Completion

After generating all documents, show a brief summary to the user:

1. List of generated files and each file's role
2. Key feature summary (3-5 bullet points)
3. Total number of phases and key phase descriptions
4. Migration file list and execution order
5. Next step guidance: "Run `/instant-dev-flow impl` to start implementation from Phase 0"

## workflow-state.md Auto-Update

Create/update `docs/workflow-state.md` when document generation completes:

- **`discover` mode**: Phase 2 = completed, Phase 3 = pending
- **`plan` mode**: Phase 3 = completed (Phase 2 should already be completed)
- **No argument / full**: Phase 2, 3 = completed
