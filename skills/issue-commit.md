# issue-commit

**Issue creation and commit automation skill.**

Generates structured GitHub issues for completed work items and produces conventional commit messages with full traceability to specifications and implementation plans.

---

## Purpose

The final mile of disciplined development is documentation. Code without linked issues is untraceable. Commits without structured messages are unsearchable. This skill ensures every piece of completed work is properly documented, linked, and discoverable.

---

## Trigger

Activated during:
- Phase 6 (Commit) of the dev-workflow
- Manual invocation for mid-workflow commits (with explicit user approval)
- Error documentation that requires an issue

---

## Actions

### 1. Analyze Changes

Review all changes since the last commit:
- Files modified, added, or deleted
- Functional requirements addressed (cross-reference with `spec.md`)
- Plan tasks completed (cross-reference with `plan.md`)

### 2. Generate GitHub Issues

For each logical unit of work, create an issue with:

```markdown
Title: <concise description of the work item>

## Description
<what was implemented and why>

## Spec Reference
- Requirement: <spec requirement ID or description>
- Acceptance Criteria: <relevant criteria from spec>

## Implementation Details
- Files changed: <list of files>
- Approach: <brief technical description>

## Verification
- [ ] Tests passing
- [ ] Review completed
- [ ] Spec compliance verified
```

**Issue labeling:**
- `feature` — New functionality
- `fix` — Bug fix
- `refactor` — Code improvement without behavior change
- `docs` — Documentation update
- `test` — Test addition or modification

### 3. Generate Commit Messages

Produce commit messages in conventional format:

```
type(scope): concise description

- Detail 1: what was done and why
- Detail 2: additional context if needed

Closes #<issue-number>
Refs: spec.md#<requirement>
```

**Commit types:**
- `feat` — New feature
- `fix` — Bug fix
- `refactor` — Code restructuring
- `test` — Test changes
- `docs` — Documentation changes
- `chore` — Build, config, tooling changes

### 4. Suggest Bilingual Messages (Optional)

When enabled, provide commit messages in both English and the team's local language:

```
feat(auth): implement JWT token refresh mechanism

- Add automatic token refresh 5 minutes before expiry
- Handle refresh failure with graceful session termination
- Store refresh tokens in httpOnly secure cookies

Closes #42
```

---

## Output Artifacts

- GitHub issues (created via `gh` CLI or API)
- Structured commit messages
- Updated `workflow-state.md` with commit references

---

## Validation

Before committing, the skill verifies:
1. All changes are staged
2. No untracked files that should be included
3. Commit message accurately reflects the changes
4. Referenced issues exist (or will be created)
5. Workflow state allows committing (all prior phases complete)

---

## Configuration

| Setting | Default | Description |
|---------|---------|-------------|
| `auto_create_issues` | `true` | Automatically create GitHub issues |
| `bilingual_messages` | `false` | Generate messages in two languages |
| `issue_labels` | `auto` | Auto-detect labels from change type |
| `link_to_spec` | `true` | Include spec references in issues |
