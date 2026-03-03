# System Design: [Project Name]

> **Version:** 0.1
> **Date:** [YYYY-MM-DD]
> **Spec Reference:** spec.md v[version]
> **Status:** Draft | In Review | Approved

---

## 1. Design Principles

| Principle | Application |
|-----------|------------|
| [e.g., Single Source of Truth] | [How it's applied in this system] |
| [e.g., Fail Fast] | [How it's applied in this system] |
| [e.g., Least Privilege] | [How it's applied in this system] |

---

## 2. Technology Stack

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Language | [e.g., TypeScript 5.x] | [Why this choice] |
| Framework | [e.g., Next.js 15 (App Router)] | [Why this choice] |
| Database | [e.g., PostgreSQL via Supabase] | [Why this choice] |
| Authentication | [e.g., Supabase Auth] | [Why this choice] |
| Styling | [e.g., Tailwind CSS + shadcn/ui] | [Why this choice] |
| Deployment | [e.g., Vercel] | [Why this choice] |
| Testing | [e.g., Vitest + Playwright] | [Why this choice] |

---

## 3. Architecture Diagram

```
[ASCII or Mermaid diagram showing major components and their interactions]

┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Client     │────▶│   Server    │────▶│   Database  │
│   (Browser)  │◀────│   (API)     │◀────│   (Store)   │
└─────────────┘     └─────────────┘     └─────────────┘
                          │
                          ▼
                    ┌─────────────┐
                    │  External   │
                    │  Services   │
                    └─────────────┘
```

---

## 4. Data Flow

### [Primary Flow Name]

```
[Step-by-step data flow through the system]

1. User action → Client
2. Client → API request → Server
3. Server → Validate → Business logic
4. Business logic → Database query
5. Database → Response → Client
6. Client → UI update
```

---

## 5. Directory Structure

```
project-root/
├── src/
│   ├── app/              # Routes and pages
│   ├── components/       # Reusable UI components
│   ├── lib/              # Business logic and utilities
│   ├── types/            # TypeScript type definitions
│   └── config/           # Configuration
├── tests/
├── docs/
└── public/               # Static assets
```

---

## 6. Architecture Decision Records (ADR)

### ADR-001: [Decision Title]

**Context:** [What prompted this decision]
**Decision:** [What was decided]
**Rationale:** [Why this option was chosen over alternatives]
**Consequences:** [Trade-offs and implications]

---

## 7. Performance Strategy

| Concern | Strategy | Target |
|---------|----------|--------|
| [e.g., Initial load] | [e.g., SSR + code splitting] | [e.g., LCP < 2.5s] |
| [e.g., API latency] | [e.g., Edge functions + caching] | [e.g., p95 < 200ms] |
| [e.g., Database] | [e.g., Connection pooling + indexes] | [e.g., Query < 50ms] |

---

## 8. Security Considerations

| Area | Measure |
|------|---------|
| Authentication | [e.g., JWT with refresh tokens, HttpOnly cookies] |
| Authorization | [e.g., Row Level Security (RLS) policies] |
| Input validation | [e.g., Zod schemas at API boundaries] |
| Data protection | [e.g., Encryption at rest, TLS in transit] |
| Secrets | [e.g., Environment variables, no hardcoded values] |

---

## Approval

| Role | Name | Date | Decision |
|------|------|------|----------|
| Author | | | |
| Reviewer | | | Approved / Rejected / Needs Revision |
