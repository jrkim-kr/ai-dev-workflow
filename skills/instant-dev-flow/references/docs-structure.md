# Docs Directory Structure

## Standard Layout

```
project-root/
├── docs/
│   ├── mvp-prd.md                        # Product Requirements Document
│   ├── spec.md                           # Feature Specification
│   ├── system-design.md                  # System Design + ADRs
│   ├── checklist.md                      # Unified Implementation Checklist
│   ├── workflow-state.md                 # Workflow State (auto-generated)
│   ├── schema/
│   │   ├── schema-overview.md            # Schema Overview (ERD)
│   │   └── migrations/
│   │       ├── 001_initial_schema.sql
│   │       ├── 002_add_indexes.sql
│   │       ├── 003_add_rls_policies.sql
│   │       ├── 004_functions_triggers.sql
│   │       ├── 005_seed_data.sql
│   │       └── README.md                 # Migration execution guide
│   ├── test/
│   │   ├── test-report.md                # Automated Test Results
│   │   └── manual-test-case.md           # Manual Test Scenarios
│   ├── guides/
│   │   ├── setup-guide-developer.md      # Developer Setup Guide
│   │   └── setup-guide-end-user.md       # End User Guide
│   └── errors/
│       └── ERR-NNN-description.md        # Error Documentation
├── tests/
│   ├── unit/                             # Unit test files
│   ├── integration/                      # Integration test files
│   ├── e2e/                              # E2E test files
│   ├── __fixtures__/                     # Test fixtures
│   └── __testdata__/                     # Test data files
└── errors/                               # (Alternative) Error docs at project root
    └── ERR-NNN-description.md
```

## File & Directory Purposes

| Path | Purpose | Phase |
|------|---------|-------|
| `docs/mvp-prd.md` | Product requirements — the "why" and "what" | Intake |
| `docs/spec.md` | Feature specification — detailed "how" per feature | Discover |
| `docs/system-design.md` | System design, ADRs — technical decisions | Discover |
| `docs/checklist.md` | Unified implementation checklist | Plan |
| `docs/workflow-state.md` | Workflow state tracker (auto-managed) | All |
| `docs/schema/` | Database schema and migrations | Discover |
| `docs/test/` | Test reports and manual test cases | Test |
| `docs/guides/` | Developer and end-user setup guides | Deliver |
| `docs/errors/` or `errors/` | Error documentation | Continuous |
| `tests/` | Test source files | Test |

## Rules

1. Core docs (mvp-prd, spec, system-design, checklist, workflow-state) live directly under `docs/` — no unnecessary subdirectories
2. Subdirectories are only used when multiple related files are expected (schema/, test/, guides/, errors/)
3. Test source files go under `tests/` (not `docs/test/`)
4. `docs/test/` contains reports and guides, not test code
5. Error docs can be at `docs/errors/` or `errors/` — pick one per project
6. `workflow-state.md` is always auto-managed — do not edit manually
