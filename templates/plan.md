# Implementation Plan: [Project Name]

> **Version:** 0.1
> **Date:** [YYYY-MM-DD]
> **Spec Reference:** spec.md v[version]
> **Status:** Draft | In Review | Approved

---

## 1. Architecture Overview

[High-level description of the system architecture. Include the primary components, their responsibilities, and how they communicate.]

```
[ASCII diagram of the architecture]
```

---

## 2. Technology Stack

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Runtime | [e.g., Node.js 20] | [Why this choice] |
| Framework | [e.g., Express / Next.js] | [Why this choice] |
| Database | [e.g., PostgreSQL 16] | [Why this choice] |
| ORM | [e.g., Prisma / Drizzle] | [Why this choice] |
| Testing | [e.g., Vitest / Jest] | [Why this choice] |
| Deployment | [e.g., Docker / Vercel] | [Why this choice] |

---

## 3. File Structure

```
project-root/
├── src/
│   ├── routes/          # API route handlers
│   ├── services/        # Business logic
│   ├── models/          # Data models and DB access
│   ├── middleware/       # Request middleware
│   ├── utils/           # Shared utilities
│   └── config/          # Configuration management
├── tests/
│   ├── unit/            # Unit tests
│   ├── integration/     # Integration tests
│   └── e2e/             # End-to-end tests
├── docs/                # Project documentation
└── scripts/             # Build and deployment scripts
```

---

## 4. Component Design

### Component: [Component Name]

**Responsibility:** [What this component does]
**Dependencies:** [What it depends on]
**Interface:**

```
[Function signatures, API surface, or interface definition]
```

**Spec Requirements Covered:** FR-001, FR-002

---

## 5. Database Schema

### Migration: 001_initial_schema

```sql
-- [DDL statements for the initial schema]
```

### Migration: 002_[description]

```sql
-- [DDL statements for subsequent changes]
```

---

## 6. Implementation Tasks

Tasks are ordered by dependency. Each task maps to one or more spec requirements.

### Phase A: Foundation

| # | Task | Spec Ref | Dependencies | Estimated Complexity |
|---|------|----------|-------------|---------------------|
| A1 | [Task description] | FR-001 | None | Low / Medium / High |
| A2 | [Task description] | FR-002 | A1 | Low / Medium / High |

### Phase B: Core Features

| # | Task | Spec Ref | Dependencies | Estimated Complexity |
|---|------|----------|-------------|---------------------|
| B1 | [Task description] | FR-003 | A1, A2 | Low / Medium / High |
| B2 | [Task description] | FR-004 | B1 | Low / Medium / High |

### Phase C: Integration & Polish

| # | Task | Spec Ref | Dependencies | Estimated Complexity |
|---|------|----------|-------------|---------------------|
| C1 | [Task description] | NFR-001 | B1, B2 | Low / Medium / High |
| C2 | [Task description] | NFR-002 | All | Low / Medium / High |

---

## 7. Risk Assessment

| Risk | Impact | Likelihood | Mitigation |
|------|--------|-----------|------------|
| [Risk description] | High / Medium / Low | High / Medium / Low | [Mitigation strategy] |

---

## 8. Open Decisions

| # | Decision | Options | Recommendation | Status |
|---|----------|---------|---------------|--------|
| 1 | [Decision needed] | [Option A, Option B] | [Recommended option] | Open |

---

## Approval

| Role | Name | Date | Decision |
|------|------|------|----------|
| Author | | | |
| Reviewer | | | Approved / Rejected / Needs Revision |
