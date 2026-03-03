# issue-commit — Issue, Commit & Push

Handles error documentation, GitHub issue creation, and commit message generation in one sequential workflow. **Phases must execute in order: 0 → 1 → 2 → 3 → 4. No skipping.**

## Phases

### Phase 0: Pre-flight (mandatory)

1. **Sync latest code**: `git pull`
2. **Check branch status**: `git status`, `git log --oneline -5`
3. **Report to user**: current branch, sync status, uncommitted changes

### Phase 1: Error Documentation

1. Analyze error: root cause, reproduction steps, context
2. Check existing error docs in `<project>/errors/` to avoid duplicates
3. Create/update error doc:

```markdown
# [ERROR_CODE] Short Description

## Summary
One-line description.

## Root Cause
Technical explanation.

## Reproduction Steps
1. Step 1 ...

## Solution
How to fix or prevent.

## Prevention Checklist
- [ ] Specific check items

## Related Files
- `path/to/file.js` — description
```

**File naming:** `ERR-001-brief-description.md`

**Error code ranges:**
- ERR-001–099: DOM/Selector
- ERR-100–199: Network/API
- ERR-200–299: Data parsing
- ERR-300–399: Authentication
- ERR-400–499: Channel-specific

### Phase 2: GitHub Issue Creation (for all change types)

Create issues for **all changes** — not just bugs. Features, refactors, docs, all types.

- Use `gh issue create` in the current repository
- Record issue number for commit message references

**Issue format by type:**

| Type | Title Format | Label |
|------|-------------|-------|
| Bug fix | `[ERROR_CODE] Short Description` | `bug` |
| Feature | `feat(scope): Short Description` | `enhancement` |
| Refactor | `refactor(scope): Short Description` | `refactor` |
| Docs | `docs(scope): Short Description` | `documentation` |

### Phase 3: Commit

1. Analyze changes: `git diff HEAD`, `git diff --cached`, `git status`
2. Group by independent project/area
3. Generate conventional commit messages
4. Present **both English and local language** messages to user
5. **Commit with English message**
6. Reference issues: `Closes #N` or `Refs #N`

**Commit guidelines:**
- First line ≤ 72 characters
- English uses imperative ("add" not "added")
- Include `Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>`

### Phase 4: Push (requires user approval)

1. Present commit summary (files, messages, issue numbers, branch)
2. Ask user: Push / Don't push / Push to different branch
3. Execute `git push` only if approved

## Fail-safe

If no changes to commit, inform user that working directory is clean.
