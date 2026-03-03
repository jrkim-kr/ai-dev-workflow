# web-project-planner — Planning & Design

Analyzes project structure and gathers requirements through interactive Q&A to generate: specification (spec.md), system design (system-design.md), DB migrations, implementation plan (plan.md), and verification checklist (checklist.md). Corresponds to Phases 1–2 of `/dev-workflow`.

## Modes

| Argument | Mode | Scope | Output |
|----------|------|-------|--------|
| (none) | Full | Phase 0 → 1 → 2 | All 5 documents |
| `plan` | Plan only | Phase 0 → 1 | spec.md, system-design.md, migrations |
| `design` | Design only | Read existing spec → Phase 2 | plan.md, checklist.md |
| `[name]` | Full | Same as (none), uses name in doc titles | All 5 documents |

> `design` mode requires `docs/spec/spec.md` and `docs/architecture/system-design.md` to exist. If missing, guides user to run `plan` first.

## Procedure

### Phase 0: Project Structure Analysis (automatic)

1. Scan project files (4 levels deep, exclude node_modules)
2. Read package.json, tsconfig.json, README.md, CLAUDE.md
3. Detect project type, framework, existing docs
4. Feed analysis results into document generation

### Phase 1: Requirements Gathering (interactive Q&A)

Uses interactive questions in 3 rounds (max 4 questions per round):

**Round 1 — Project basics:** Project type, user roles, core features, project name
**Round 2 — Tech stack:** Frontend framework, backend/DB, deployment, UI styling
**Round 3 — Detailed features:** Notifications, external integrations, design concept, additional requirements

Follow-up questions if ambiguous: core business flow, per-role access scope, data relationships, special business rules.

### Phase 2: Document Generation

#### Doc 1: `docs/spec/spec.md` — Feature Specification (`plan` mode)

Structure:
- System overview (name, purpose, user types, tech stack)
- Core state flow (ASCII diagram + state definitions)
- Per-page/feature detailed specs (input fields, post-submit behavior, UI guide, constraints)
- Notification/messaging system (if applicable)
- External integrations (if applicable)
- Non-functional requirements (responsive, accessibility, performance, security, SEO, deployment)
- Page map (ASCII tree of all URLs)
- Future expansion considerations (post-MVP)

#### Doc 2: `docs/architecture/system-design.md` — System Design (`plan` mode)

Structure:
- Design principles
- Technology stack (with rationale per choice)
- Architecture overview (ASCII diagram)
- Data flow (ASCII diagram)
- Directory structure (based on Phase 0 analysis)
- Key design decisions (ADR format)
- Performance strategy
- Security considerations

#### Doc 3: DB Migrations (`plan` mode)

Directory structure under `docs/schema/`:
```
docs/schema/
├── schema-overview.md
└── migrations/
    ├── 001_initial_schema.sql
    ├── 002_add_indexes.sql
    ├── 003_add_rls_policies.sql
    ├── 004_functions_triggers.sql
    ├── 005_seed_data.sql
    └── README.md
```

Each migration file includes UP (apply) and DOWN (rollback) sections.

#### Doc 4: `docs/plan/plan.md` — Implementation Plan (`design` mode)

Structure:
- Phase 0: Project setup (init, DB connection, migration, design system)
- Phase 1–N: Feature implementation (numbered tasks, API endpoints, UI components)
- Final Phase: Deployment & QA
- Implementation order summary (ASCII diagram)

Rules: Each phase is independently functional. Core user features first, admin next, external integrations later, deployment last.

#### Doc 5: `docs/plan/checklist.md` — Verification Checklist (`design` mode)

- 3–5 check items per plan.md step
- Ordered: file existence → feature behavior → data integrity
- All items numbered (#N) globally
- Includes final comprehensive verification section (spec compliance, performance, build)

## Output Paths

```
docs/
├── spec/spec.md
├── architecture/system-design.md
├── schema/
│   ├── schema-overview.md
│   └── migrations/*.sql
└── plan/
    ├── plan.md
    ├── checklist.md
    └── workflow-state.md
```

## After Completion

Updates `docs/plan/workflow-state.md`:
- `plan` mode: Phase 1 = completed
- `design` mode: Phase 2 = completed
- Full mode: Phase 1 + 2 = completed
