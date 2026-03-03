# spec-writer

**Specification authoring skill for structured requirement capture.**

Guides the creation of complete, unambiguous project specifications that serve as the single source of truth throughout the development workflow.

---

## Purpose

Every workflow failure traces back to a weak specification. Vague requirements produce vague implementations. Missing edge cases become production bugs. Undefined constraints lead to architectural decisions that don't scale. This skill enforces specification quality at the point of creation.

---

## Trigger

Activated during:
- Phase 1 (Plan) of the dev-workflow
- Manual invocation for spec creation or revision
- When requirements need to be formalized from informal input

---

## Actions

### 1. Requirements Elicitation

Analyze the input (user description, existing codebase, reference materials) and extract:
- **What the system must do** (functional requirements)
- **How well it must do it** (non-functional requirements)
- **What it must not do** (constraints and exclusions)
- **Who interacts with it** (actors and roles)

### 2. Structured Specification Generation

Produce `spec.md` following the specification template with all sections:

#### Required Sections

1. **Overview** — One-paragraph project description
2. **Actors** — Who uses the system, their roles and permissions
3. **Functional Requirements** — Numbered, testable requirements
4. **Non-Functional Requirements** — Performance, security, scalability constraints
5. **Data Model** — Entities, relationships, constraints
6. **API Contracts** — Endpoints, request/response formats, error codes
7. **Acceptance Criteria** — Measurable conditions for each requirement
8. **Out of Scope** — Explicit exclusions to prevent scope creep
9. **Open Questions** — Unresolved items requiring stakeholder input

### 3. Quality Validation

Before presenting the spec for approval, validate:

- [ ] Every functional requirement has at least one acceptance criterion
- [ ] No requirement uses vague language ("fast", "user-friendly", "scalable" without metrics)
- [ ] Data model covers all entities referenced in requirements
- [ ] API contracts align with functional requirements
- [ ] No contradictions between requirements
- [ ] Out of scope section explicitly addresses common assumptions
- [ ] Open questions are clearly stated with proposed answers where possible

### 4. Spec Presentation

Present the completed spec to the user with:
- Summary of key decisions made during authoring
- Highlighted areas that need user confirmation
- List of assumptions made (with rationale)
- Open questions that block further progress

---

## Specification Quality Criteria

| Criterion | Definition | Test |
|-----------|-----------|------|
| **Complete** | All known requirements are captured | No requirement exists only in someone's head |
| **Unambiguous** | Each requirement has one interpretation | Two engineers would implement it the same way |
| **Testable** | Each requirement can be verified | An acceptance criterion exists and is measurable |
| **Consistent** | No requirements contradict each other | Cross-reference check passes |
| **Traceable** | Each requirement has a unique identifier | Every requirement can be referenced by ID |
| **Prioritized** | Requirements indicate relative importance | Must-have vs. nice-to-have is explicit |

---

## Output Artifacts

- `spec.md` — Complete project specification
- Updated `workflow-state.md` with spec status

---

## Common Anti-Patterns

**Avoid these in specifications:**

- "The system should be fast" → Instead: "API responses must return within 200ms at p95 under 1000 concurrent users"
- "Users can manage their data" → Instead: "Users can create, read, update, and delete their own profile data. Users cannot access other users' data."
- "Handle errors appropriately" → Instead: "Return HTTP 400 with error code and message for validation failures. Return HTTP 500 with correlation ID for server errors. Log all 500 errors with full stack trace."
- Omitting the out-of-scope section → Always state what you're NOT building to prevent assumption-driven scope creep
