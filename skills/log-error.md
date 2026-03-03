# log-error — Error Documentation

Documents errors to prevent recurrence. Creates structured error reports and GitHub issues for tracking.

## Workflow

1. **Analyze** the error: root cause, reproduction steps, context (function, input values)
2. **Check** existing error docs in `<project>/errors/` to avoid duplicates
3. **Create/update** error documentation:

```markdown
# [ERROR_CODE] Short Description

## Summary
One-line description.

## Root Cause
Technical explanation of why this error occurs.

## Reproduction Steps
1. Step 1
2. Step 2

## Solution
How to fix or prevent this error.

## Prevention Checklist
- [ ] Specific check to avoid this error

## Related Files
- `path/to/file.js` — brief description
```

4. **File naming:** `ERR-001-brief-description.md`
5. **Create GitHub issue:** `gh issue create --title "[ERROR_CODE] Description" --label "bug"`

## Error Code Ranges

| Range | Category |
|-------|----------|
| ERR-001–099 | DOM/Selector errors |
| ERR-100–199 | Network/API errors |
| ERR-200–299 | Data parsing errors |
| ERR-300–399 | Authentication errors |
| ERR-400–499 | Channel-specific errors |

## When Writing Code

Before implementing any feature or fix:
1. Check relevant `<project>/errors/` for documented errors
2. Apply prevention checklists from related error docs
3. Reference error doc in code comments if directly related

## Fail-safe

If error cannot be fully diagnosed, document what is known and mark `Status: Under Investigation`.
