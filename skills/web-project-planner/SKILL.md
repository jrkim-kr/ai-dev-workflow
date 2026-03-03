---
name: web-project-planner
description: "Before building a new website, analyze the project structure and ask about requirements and tech stack to generate a feature spec (spec.md), system design doc (system-design.md), DB migrations (migrations/), implementation plan (plan.md), and verification checklist (checklist.md). Corresponds to Phase 1-2 of /dev-workflow."
argument-hint: "[plan | design | project-name (optional)]"
---

# Web Project Planner Skill

## Purpose

Before starting a new website project, analyze the project structure and collect requirements through interactive Q&A with the user to generate a **feature specification**, **system design document**, **DB migrations**, **step-by-step implementation plan**, and **verification checklist**.

> **Note:** This skill corresponds to Phase 1 (Planning) + Phase 2 (Design) of `/dev-workflow`.

## Execution Modes

| Argument | Mode | Scope | Generated Documents |
|----------|------|-------|-------------------|
| (none) | Full | Phase 0 → Phase 1 → Phase 2 complete | spec.md, system-design.md, migrations, plan.md, checklist.md |
| `plan` | Planning | Phase 0 → Phase 1 → Phase 2 (docs 1-3 only) | spec.md, system-design.md, migrations |
| `design` | Design | Read existing spec.md → Phase 2 (docs 4-5 only) | plan.md, checklist.md |
| `[project-name]` | Full | Same as (none), uses project name in document titles | All |

> `design` mode requires `docs/spec/spec.md` and `docs/architecture/system-design.md` to already exist. If missing, guide user to run `plan` mode first.

## Execution Steps

### Phase 0: Project Structure Analysis (Automatic)

Before generating documents, apply the analysis logic from `/project-structure` skill to understand the current project state.

1. **Parallel scan:**
   - `**/*` glob (4 levels, excluding node_modules, etc.)
   - Read `package.json`, `tsconfig.json`, `README.md`, `CLAUDE.md`
   - Check monorepo indicators (workspaces, turbo.json, nx.json)
   - Check existing `docs/` directory

2. **Detection targets:**
   - Project type & framework (Next.js, React, Vue, etc.)
   - Existing directory structure and conventions
   - Already existing documents (existing spec, plan, etc.)
   - Tech stack in use

3. **Reflect structure analysis in subsequent document generation:**
   - Reflect actual structure in system-design.md directory structure section
   - Reflect current project state in plan.md Phase 0
   - If existing documents are found, confirm with user before overwriting

---

### Phase 1: Requirements Gathering (Interactive Q&A)

Use the AskUserQuestion tool to ask questions by category. Bundle up to 4 questions at a time. Proceed with follow-up questions based on user responses.

#### Round 1: Project Basic Information

Ask the following 4 questions:

1. **Project Type**
   - header: "Project Type"
   - question: "What kind of website are you building?"
   - options:
     - "Corporate/Brand Site" — Company/brand intro, portfolio
     - "Booking/Order Management System" — Reservations, orders, scheduling
     - "Community/Forum" — User community, forum, blog
     - "Dashboard/Admin System" — Data management, admin panel

2. **User Types**
   - header: "User Types"
   - question: "What types of users will use the system? (Multiple selection allowed)"
   - multiSelect: true
   - options:
     - "General Users (Customers)" — Regular customers using the service
     - "Administrators (Operators)" — System operators/managers
     - "Staff/Employees" — Internal staff with limited permissions
     - "Anonymous Visitors" — Visitors without login

3. **Core Feature**
   - header: "Core Feature"
   - question: "What is the most important core feature?"
   - options:
     - "Booking/Scheduling" — Date/time-based reservation management
     - "Product/Service Management" — Product registration, catalog, pricing
     - "User Management/Auth" — Sign-up, login, permission management
     - "Content Management" — Posts, announcements, page management

4. **Project Name**
   - header: "Project Name"
   - question: "What is the project name (or service name)?"
   - options:
     - "Not decided yet" — Will decide later
     - "Enter manually" — Enter the project name directly

#### Round 2: Tech Stack & Infrastructure

If Phase 0 detected an existing tech stack, present it as the default. Ask the following 4 questions:

1. **Frontend Framework**
   - header: "Framework"
   - question: "Which frontend framework will you use?"
   - options:
     - "Next.js (App Router) (Recommended)" — React-based, SSR/SSG, full-stack
     - "React + Vite" — SPA, client-side rendering
     - "Nuxt.js" — Vue.js-based full-stack framework
     - "Astro" — Content-focused static sites

2. **Backend/Database**
   - header: "Backend/DB"
   - question: "How will you set up the backend and database?"
   - options:
     - "Supabase (PostgreSQL) (Recommended)" — BaaS, integrated auth/DB/storage
     - "Firebase (Firestore)" — Google BaaS, NoSQL
     - "Dedicated Backend Server (Express/NestJS)" — Node.js-based custom API server
     - "Enter manually" — Other tech stack

3. **Deployment Environment**
   - header: "Deployment"
   - question: "Where will you deploy?"
   - options:
     - "Vercel (Recommended)" — Next.js optimized, auto deploy
     - "AWS (EC2/ECS)" — Self-managed infrastructure
     - "Cloudflare Pages" — Edge-based deployment
     - "Enter manually" — Other deployment environment

4. **UI Styling**
   - header: "Styling"
   - question: "How will you handle UI styling?"
   - options:
     - "Tailwind CSS + shadcn/ui (Recommended)" — Utility CSS + component library
     - "Tailwind CSS" — Utility-based CSS only
     - "Material UI (MUI)" — Google Material Design components
     - "Enter manually" — Other UI framework

#### Round 3: Detailed Feature Requirements

Ask about detailed features based on Round 1 responses. Question content is dynamically composed based on project type.

Example questions (select based on project type):

1. **Notification/Messaging System**
   - header: "Notifications"
   - question: "Do you need notification/messaging features? (Multiple selection allowed)"
   - multiSelect: true
   - options:
     - "KakaoTalk Notifications" — Kakao business messages
     - "Email Notifications" — Email sending
     - "SMS" — Text message sending
     - "Not needed" — No notification features required

2. **External Service Integration**
   - header: "Integrations"
   - question: "Are there external services you need to integrate? (Multiple selection allowed)"
   - multiSelect: true
   - options:
     - "Google Calendar" — Calendar/schedule integration
     - "Google Sheets" — Spreadsheet data integration
     - "Payments (Toss Payments/KakaoPay)" — Online payments
     - "Not needed" — No external integrations required

3. **Design Concept**
   - header: "Design"
   - question: "Do you have a design concept or reference site?"
   - options:
     - "Minimal/Simple" — Clean, no-frills design
     - "Modern/Trendy" — Contemporary, polished design
     - "Have a reference site" — There's a benchmark site (enter manually)
     - "Designer will provide" — Design files from Figma/Zeplin etc.

4. **Additional Requirements**
   - header: "Other"
   - question: "Any additional features or special considerations? (Multiple selection allowed)"
   - multiSelect: true
   - options:
     - "Responsive (Mobile-first)" — Mobile optimization required
     - "Multi-language support" — Languages beyond primary needed
     - "SEO Optimization" — Search engine optimization needed
     - "Enter manually" — Other requirements

#### Follow-up Questions (As Needed)

If user responses are unclear or additional information is needed, use AskUserQuestion in free form for follow-up.

Specifically, always ask follow-up when the following are unclear:
- Core business flow (e.g., state flow like booking → approval → confirmation)
- Accessible pages and feature scope per user type
- Data relationships (e.g., 1:N, N:M relationships)
- Special business rules or constraints

---

### Phase 2: Document Generation

Generate documents under the `docs/` directory based on collected requirements. Follow the folder structure rules from the `project-docs` skill.

- **`plan` mode**: Generate docs 1-3 only (spec.md, system-design.md, migrations)
- **`design` mode**: Generate docs 4-5 only (plan.md, checklist.md) — skip Phases 0-1 and read existing spec.md
- **No argument / project name**: Generate all docs 1-5

#### Document 1: `docs/spec/spec.md` (Feature Specification) — included in `plan` mode

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

#### Document 2: `docs/architecture/system-design.md` (System Design Document) — included in `plan` mode

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

#### Document 3: DB Migrations (migration structure) — included in `plan` mode

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

#### Document 4: `docs/plan/plan.md` (Step-by-Step Implementation Plan) — included in `design` mode

> In `design` mode: Read existing `docs/spec/spec.md` and `docs/architecture/system-design.md` to generate this document.

Follow this structure:

```markdown
# [Project Name] — Step-by-Step Implementation Plan

> Total N phases · Each phase is an independently functional unit

---

## Phase 0. Project Initial Setup

### 0-1. Project Creation
- Framework initialization command
- Folder structure setup
- Linter/formatter configuration

### 0-2. Backend/DB Connection
- DB project creation and connection
- Environment variable setup
- Client utility files

### 0-3. DB Migration Execution
- Execute migrations/ files sequentially
- Create admin account

### 0-4. Design System Foundation
- Theme setup
- Common components
- Font configuration

**Completion Criteria**: [Specific criteria to determine this phase is complete]

---

## Phase 1~N. Feature Implementation

[For each Phase]:
- Numbered list of detailed tasks
- Specific implementation content for each task
- API endpoint specifications (method path — description)
- UI component list
- Additional migration files if needed

**Completion Criteria**: [Specific completion criteria]

---

## Final Phase. Deployment & Wrap-up

### Deployment Configuration
### Production Data Setup
### QA Checklist (checkbox format)

**Completion Criteria**: Production-ready state

---

## Implementation Order Summary

[Visualize inter-phase relationships with ASCII diagram]
```

**Implementation plan writing rules:**
- Each phase is an independently functional unit
- Phase 0 is always project initial setup
- Core user features first, admin features next
- External integrations after core features are complete
- Deployment/QA in the final phase
- Completion criteria must be specific and verifiable

#### Document 5: `docs/plan/checklist.md` (Verification Checklist) — included in `design` mode

Auto-generate check items corresponding to each step in plan.md:

```markdown
# [Project Name] — Implementation Checklist

> Auto-generated from plan.md | Last modified: [Date]

## Usage
- After implementing each step, check the corresponding item with `[x]`
- Order: File existence → Feature operation → Data integrity

---

## Phase 0: Project Initial Setup

### Step 0-1: Project Creation
- [ ] #1 Verify project directory creation
- [ ] #2 Verify package.json dependencies installed
- [ ] #3 Verify dev server runs successfully

### Step 0-3: DB Migration
- [ ] #N 001_initial_schema.sql executed successfully
- [ ] #N+1 002_add_indexes.sql executed successfully
- [ ] #N+2 Seed data insertion verified

...

---

## Phase 1~N: [Phase Name]

### Step N-1: [Step Name]
- [ ] #N File creation/modification verified: `src/...`
- [ ] #N+1 Feature operation verified: [specific behavior description]
- [ ] #N+2 Error handling verified: [error scenario]

---

## Comprehensive Verification

### spec.md Compliance Check
- [ ] All pages implemented
- [ ] All state flows implemented
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
- 3-5 check items per plan.md step
- Order: File existence → Feature operation → Data integrity
- Sequential numbering (#N) for all items
- Comprehensive verification section includes spec.md compliance, performance, and build checks

---

## Writing Rules (Common)

1. **Use clear language**: Technical terms may include both English and localized terms where helpful.
2. **Use tables extensively**: Use markdown tables for listing items.
3. **ASCII diagrams**: Visualize state flows, page maps, and implementation order with ASCII diagrams.
4. **Cross-document consistency**: Terminology, table names, status codes, and page paths must be consistent across all documents.
5. **Practicality**: Write content at a level that can be directly used for implementation.
6. **Message templates**: If notification/messaging features exist, include actual message content as templates.
7. **Follow project-docs conventions**: Filenames in lowercase-kebab-case.md, folder structure uses `docs/spec/`, `docs/architecture/`, `docs/plan/`, `docs/schema/`.

## File Paths

```
docs/
├── spec/
│   └── spec.md                    ← Feature specification
├── architecture/
│   └── system-design.md           ← System design document
├── schema/
│   ├── schema-overview.md         ← Schema overview
│   └── migrations/
│       ├── 001_initial_schema.sql
│       ├── 002_add_indexes.sql
│       ├── ...
│       └── README.md              ← Migration guide
└── plan/
    ├── plan.md                    ← Implementation roadmap
    ├── checklist.md               ← Verification checklist
    └── workflow-state.md          ← Workflow state (auto-generated)
```

- If a project name is given as argument, use it in document titles
- Create directories if they don't exist

## After Completion

After generating all documents, show a brief summary to the user:

1. List of generated files and each file's role
2. Key feature summary (3-5 bullet points)
3. Total number of phases and key phase descriptions
4. Migration file list and execution order
5. Next step guidance: "Run `/dev-workflow impl` to start implementation from Phase 0"

## workflow-state.md Auto-Update

Create/update `docs/plan/workflow-state.md` when document generation completes:

- **`plan` mode**: Phase 1 = completed, Phase 2 = pending
- **`design` mode**: Phase 2 = completed (Phase 1 should already be completed)
- **No argument / full**: Phase 1, 2 = completed
