# Specification: [Project Name]

> **Version:** 0.1
> **Date:** [YYYY-MM-DD]
> **Author:** [Name]
> **Status:** Draft | In Review | Approved

---

## 1. Overview

[One paragraph describing what this project does, who it's for, and why it exists. Keep it concrete.]

---

## 2. Actors

| Actor | Role | Permissions |
|-------|------|-------------|
| [Actor Name] | [Description of role] | [What they can do] |

---

## 3. Functional Requirements

### FR-001: [Requirement Title]

**Description:** [What the system must do]
**Priority:** Must-have | Should-have | Nice-to-have
**Acceptance Criteria:**
- [ ] [Measurable condition 1]
- [ ] [Measurable condition 2]

### FR-002: [Requirement Title]

**Description:** [What the system must do]
**Priority:** Must-have | Should-have | Nice-to-have
**Acceptance Criteria:**
- [ ] [Measurable condition 1]
- [ ] [Measurable condition 2]

---

## 4. Non-Functional Requirements

### NFR-001: Performance

- [Specific, measurable performance target. e.g., "API response time < 200ms at p95 under 1000 concurrent users"]

### NFR-002: Security

- [Specific security requirements. e.g., "All API endpoints require JWT authentication. Passwords stored with bcrypt, min cost factor 12"]

### NFR-003: Scalability

- [Specific scalability targets. e.g., "System must handle 10x current load without architecture changes"]

---

## 5. Data Model

### Entity: [Entity Name]

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| id | UUID | PK, auto-generated | Unique identifier |
| [field] | [type] | [constraints] | [description] |
| created_at | timestamp | NOT NULL, default NOW | Creation timestamp |
| updated_at | timestamp | NOT NULL, auto-update | Last modification timestamp |

### Relationships

- [Entity A] has many [Entity B]
- [Entity B] belongs to [Entity A]

---

## 6. API Contracts

### [METHOD] /api/[resource]

**Description:** [What this endpoint does]
**Authentication:** Required | Public
**Rate Limit:** [N] requests per [time period]

**Request:**
```json
{
  "field": "type — description"
}
```

**Response (200):**
```json
{
  "field": "type — description"
}
```

**Error Responses:**
| Status | Code | Description |
|--------|------|-------------|
| 400 | VALIDATION_ERROR | [When this occurs] |
| 401 | UNAUTHORIZED | [When this occurs] |
| 404 | NOT_FOUND | [When this occurs] |

---

## 7. Acceptance Criteria Summary

| ID | Requirement | Criteria | Testable |
|----|------------|----------|----------|
| FR-001 | [Title] | [Key criterion] | Yes/No |
| FR-002 | [Title] | [Key criterion] | Yes/No |

---

## 8. Out of Scope

The following are explicitly **not** included in this version:

- [Feature/capability that might be assumed but is excluded]
- [Feature/capability that might be assumed but is excluded]

---

## 9. Open Questions

| # | Question | Proposed Answer | Status |
|---|----------|----------------|--------|
| 1 | [Unresolved question] | [Proposed answer if any] | Open |

---

## Approval

| Role | Name | Date | Decision |
|------|------|------|----------|
| Author | | | |
| Reviewer | | | Approved / Rejected / Needs Revision |
