# log-error

**Error documentation skill for preventing recurrence.**

Captures errors encountered during development, documents root causes, and creates reference entries that inform future coding decisions. Turns debugging sessions into institutional knowledge.

---

## Purpose

Errors repeat. The same null reference, the same async timing issue, the same misconfigured environment variable — they come back because no one documented them the first time. This skill ensures every significant error gets documented in a structured format that both humans and AI assistants can reference.

---

## Trigger

Activated when:
- An error is encountered during any workflow phase
- The user explicitly requests error documentation
- A recurring error pattern is detected

---

## Actions

### 1. Capture Error Context

Collect the following information:
- **Error message:** Exact error output
- **Stack trace:** Full trace if available
- **Environment:** OS, runtime version, relevant configuration
- **Phase:** Which workflow phase the error occurred in
- **Trigger:** What action caused the error

### 2. Analyze Root Cause

Determine the root cause by examining:
- Code that produced the error
- Configuration state at the time
- Dependencies and their versions
- Environmental factors

Classify the root cause:
- `logic` — Incorrect code logic
- `config` — Misconfiguration
- `dependency` — External dependency issue
- `environment` — Environment-specific problem
- `data` — Unexpected data format or state
- `concurrency` — Race condition or timing issue

### 3. Document the Error

Create an error log entry in `docs/errors/` with the following structure:

```markdown
# Error: <Short Description>

**Date:** <ISO date>
**Phase:** <workflow phase>
**Category:** <root cause category>
**Severity:** critical | major | minor

## Error Output
<exact error message and relevant stack trace>

## Root Cause
<explanation of why the error occurred>

## Fix
<what was done to resolve it>

## Prevention
<how to prevent this error in the future>

## Related
<links to related errors, issues, or documentation>
```

### 4. Cross-Reference

- Link the error to the relevant spec requirement (if applicable)
- Link to the commit that introduced the fix
- Update any affected templates or checklists to include prevention steps

---

## Output Artifacts

- Error log entry in `docs/errors/{date}-{short-description}.md`
- Updated checklist items (if prevention steps apply to future development)

---

## Integration with Workflow

During the **Implement** and **Test** phases, the AI assistant should:
1. Check existing error logs before implementing similar functionality
2. Reference known error patterns when writing new code
3. Add prevention checks to test cases for documented error categories

This creates a feedback loop: errors inform tests, tests prevent recurrence.

---

## Best Practices

- Document errors immediately, not after the fact
- Be specific about root causes — "it didn't work" is not a root cause
- Include the fix, not just the problem
- Write prevention steps that are actionable, not generic
- Link errors to spec requirements when the error reveals a spec gap
