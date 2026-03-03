# code-review-expert — Code Review

Structured review of current git changes with a senior engineer lens. Detects SOLID violations, security risks, and proposes actionable improvements. Default to review-only output unless user asks to implement changes.

## Severity Levels

| Level | Name | Description | Action |
|-------|------|-------------|--------|
| **P0** | Critical | Security vulnerability, data loss risk, correctness bug | Must block merge |
| **P1** | High | Logic error, significant SOLID violation, performance regression | Should fix before merge |
| **P2** | Medium | Code smell, maintainability concern, minor SOLID violation | Fix in this PR or create follow-up |
| **P3** | Low | Style, naming, minor suggestion | Optional improvement |

## Workflow

### 1) Preflight context

- Use `git status -sb`, `git diff --stat`, and `git diff` to scope changes
- Use `rg`/`grep` to find related modules, usages, and contracts
- Identify entry points, ownership boundaries, and critical paths (auth, payments, data writes, network)

Edge cases: No changes → ask user. Large diff (>500 lines) → summarize by file first, review in batches. Mixed concerns → group findings by logical feature.

### 2) SOLID + architecture smells

- **SRP**: Overloaded modules with unrelated responsibilities
- **OCP**: Frequent edits to add behavior instead of extension points
- **LSP**: Subclasses that break expectations or require type checks
- **ISP**: Wide interfaces with unused methods
- **DIP**: High-level logic tied to low-level implementations

When proposing refactors, explain *why* it improves cohesion/coupling. For non-trivial refactors, propose incremental plan.

### 3) Removal candidates

- Identify unused, redundant, or feature-flagged-off code
- Distinguish **safe delete now** vs **defer with plan**
- Provide follow-up plan with concrete steps

### 4) Security and reliability scan

- XSS, injection (SQL/NoSQL/command), SSRF, path traversal
- AuthZ/AuthN gaps, missing tenancy checks
- Secret leakage in logs/env/files
- Rate limits, unbounded loops, CPU/memory hotspots
- Race conditions, check-then-act, TOCTOU, missing locks
- Unsafe deserialization, weak crypto, insecure defaults

### 5) Code quality scan

- **Error handling**: swallowed exceptions, overly broad catch, missing async error handling
- **Performance**: N+1 queries, CPU-intensive ops in hot paths, missing cache
- **Boundary conditions**: null/undefined handling, empty collections, numeric boundaries, off-by-one

### 6) Output format

```markdown
## Code Review Summary

**Files reviewed**: X files, Y lines changed
**Overall assessment**: [APPROVE / REQUEST_CHANGES / COMMENT]

---

## Findings

### P0 - Critical
(none or list)

### P1 - High
1. **[file:line]** Brief title
  - Description of issue
  - Suggested fix

### P2 - Medium / P3 - Low
...

---

## Removal/Iteration Plan
(if applicable)
```

### 7) Next steps confirmation

Present findings count (P0/P1/P2/P3) and ask user:
1. Fix all
2. Fix P0/P1 only
3. Fix specific items
4. No changes

**Do NOT implement changes until user explicitly confirms.**

## Acknowledgments

Inspired by [sanyuan0704/sanyuan-skills](https://github.com/sanyuan0704/sanyuan-skills) and its `code-review-expert` skill for structured, multi-dimensional code review.
