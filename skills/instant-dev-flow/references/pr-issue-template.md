# GitHub PR & Issue Templates

## Issue Template

### Bug Report (`bug_report.md`)

```markdown
---
name: Bug Report
about: Report a bug to help us improve
title: "fix(<scope>): <short description>"
labels: bug
assignees: ''
---

## Summary
[Brief description of the bug]

## Reproduction Steps
1. Go to '...'
2. Click on '...'
3. See error

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Environment
- OS: [e.g., macOS 15.2]
- Browser: [e.g., Chrome 130]
- Version: [e.g., v1.2.0]

## Screenshots
[If applicable]

## Related Error Doc
[Link to errors/ERR-NNN-description.md if exists]
```

### Feature Request (`feature_request.md`)

```markdown
---
name: Feature Request
about: Suggest a new feature
title: "feat(<scope>): <short description>"
labels: enhancement
assignees: ''
---

## Summary
[Brief description of the feature]

## Problem
[What problem does this solve?]

## Proposed Solution
[How should it work?]

## Alternatives Considered
[Other approaches considered]

## Acceptance Criteria
- [ ] [Criterion 1]
- [ ] [Criterion 2]

## Additional Context
[Any other context or screenshots]
```

### Chore/Task (`task.md`)

```markdown
---
name: Task
about: General development task
title: "<type>(<scope>): <short description>"
labels: ''
assignees: ''
---

## Summary
[What needs to be done]

## Tasks
- [ ] [Task 1]
- [ ] [Task 2]

## Related Files
- `path/to/file`
```

---

## Pull Request Template (`pull_request_template.md`)

```markdown
## Summary
<!-- 1-3 bullet points describing what this PR does -->

-

## Related Issue
<!-- Link to the GitHub issue -->
Closes #

## Changes
<!-- List the key changes -->

-

## Type of Change
- [ ] Bug fix (non-breaking change that fixes an issue)
- [ ] New feature (non-breaking change that adds functionality)
- [ ] Breaking change (fix or feature that causes existing functionality to change)
- [ ] Documentation update
- [ ] Refactor (no functional changes)
- [ ] Chore (dependencies, config, etc.)

## Test Plan
<!-- How was this tested? -->

- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing completed

## Checklist
- [ ] Code follows project conventions
- [ ] Self-review completed
- [ ] No new warnings or errors
- [ ] Documentation updated (if applicable)

## Screenshots
<!-- If UI changes, add before/after screenshots -->

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

---

## Label Convention

| Label | Color | Used For |
|-------|-------|----------|
| `bug` | `#d73a4a` | Bug fixes |
| `enhancement` | `#a2eeef` | New features |
| `documentation` | `#0075ca` | Docs only |
| `refactor` | `#e4e669` | Code restructuring |
| `style` | `#fef2c0` | Formatting changes |
| `test` | `#bfd4f2` | Test additions |
| `chore` | `#ededed` | Config/deps |
| `performance` | `#f9d0c4` | Performance improvements |
| `P0-critical` | `#b60205` | Must fix immediately |
| `P1-high` | `#d93f0b` | Fix before release |
| `P2-medium` | `#fbca04` | Should fix |
| `P3-low` | `#0e8a16` | Nice to fix |
