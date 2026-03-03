---
name: issue-commit
description: Document errors, create GitHub issues for all changes, and suggest commit messages in English & Korean
user-invokable: true
disable-model-invocation: false
---

You handle error documentation, GitHub issue creation, and commit message generation — all in one workflow.

**You MUST follow the order PHASE 0 → PHASE 1 → PHASE 2 → PHASE 3 → PHASE 4. No phase may be skipped.**

---

# PHASE 0: Pre-Work Process (Required)

Before starting any work, **always** perform the following steps. This phase must not be skipped.

## Workflow

1. **Sync latest code:**
   ```bash
   git pull
   ```
   - If conflicts arise, inform the user and guide resolution
   - If pull fails, diagnose the cause and report to user

2. **Check current branch status:**
   ```bash
   git status
   git log --oneline -5
   ```
   - Alert the user if there are uncommitted changes
   - Verify the current working branch is correct

3. **Report status to user:**
   - Current branch name
   - Whether latest sync is complete
   - Whether there are uncommitted changes

> **This phase must be completed before proceeding to the next phase (error documentation, issue creation, commit).**

---

# PHASE 1: ERROR DOCUMENTATION

## Workflow

1. **Analyze the error:**
   - Identify root cause
   - Determine reproduction steps
   - Record context (channel, function, input values)

2. **Check existing error docs** (in `errors/` to avoid duplicates):
   - Search the project's `errors/` directory for existing error documents

3. **Create/update error documentation** (in the appropriate `<project>/errors/`):

```markdown
# [ERROR_CODE] Short Description

## Summary
One-line description of the error.

## Root Cause
Technical explanation of why this error occurs.

## Reproduction Steps
1. Step 1
2. Step 2
3. ...

## Solution
How to fix or prevent this error.

## Prevention Checklist
- [ ] Specific check to avoid this error
- [ ] Another check if applicable

## Related Files
- `path/to/file.js` - brief description
```

4. **Filename convention:** `ERR-001-brief-description.md`

## Error Code Ranges

- ERR-001 ~ ERR-099: DOM/Selector errors
- ERR-100 ~ ERR-199: Network/API errors
- ERR-200 ~ ERR-299: Data parsing errors
- ERR-300 ~ ERR-399: Authentication errors
- ERR-400 ~ ERR-499: Channel-specific errors

## Fail-Safe

If an error cannot be fully diagnosed, document what is known and mark it as `Status: Under Investigation`.

---

# PHASE 2: GITHUB ISSUE CREATION (Required for all change types)

Create a GitHub Issue for **all changes** — not just errors/bugs, but also new features, refactoring, documentation changes, and more.

## Issue Creation Rules

- Use `gh issue create` to create an issue in the current repository
- Record the issue number after creation (e.g., `#12`) — used for commit message references

## Issue Format by Change Type

### Bug/Error Fix (fix)
- Title: `[ERROR_CODE] Short Description` (if error doc exists)
- Label: `bug`
- Body: Include Summary, Root Cause, Solution from error doc

### New Feature (feat)
- Title: `feat(scope): Short Description`
- Label: `enhancement`
- Body: Feature description, changes made, related files

### Refactoring (refactor)
- Title: `refactor(scope): Short Description`
- Label: `refactor`
- Body: Refactoring rationale, changes made

### Documentation (docs)
- Title: `docs(scope): Short Description`
- Label: `documentation`
- Body: Description of documentation changes

### Other (chore, style, test, perf)
- Title: `type(scope): Short Description`
- Label: appropriate label for the type (omit if none exists)
- Body: Summary of changes

## Body Template

```markdown
## Summary
Brief description of the changes.

## Changes
- Change 1
- Change 2

## Related Files
- `path/to/file` - description
```

## Examples

```bash
# New feature
gh issue create --title "feat(slack): add per-channel Slack notifications" \
  --body "## Summary\n..." \
  --label "enhancement"

# Bug fix
gh issue create --title "[ERR-101] API timeout on search request" \
  --body "## Summary\n..." \
  --label "bug"
```

---

# PHASE 3: COMMIT (Commit in English, show both English & Korean)

## Workflow

1. **Analyze changes:**
   - `git diff HEAD` — view all changes
   - `git diff --cached` — view staged changes only
   - `git status` — understand overall state

2. **Group by logical area:**
   - If monorepo, classify by package/project directory
   - Root-level changes (e.g., README.md, configs) grouped separately

3. **Conventional Commits format:**
   - `feat`: New feature
   - `fix`: Bug fix
   - `docs`: Documentation changes
   - `refactor`: Code refactoring
   - `style`: Formatting, no code change
   - `test`: Adding or updating tests
   - `chore`: Maintenance tasks

4. **Check related GitHub issues:**
   - Run `gh issue list --state open --limit 20` to check open issues
   - Include issue references for error-doc-related changes
   - `Closes #N` — if the commit fully resolves the issue
   - `Refs #N` — if related but doesn't fully resolve it

5. **Show both English & Korean commit messages to the user, then commit with the English message:**
   - Stage relevant files with `git add`
   - Run `git commit` with the English message
   - If separate commits are needed per project, perform each one
   - Push is handled in PHASE 4 after user approval

## Output Format

For each logical group, show both English & Korean messages then commit:

```
## [Folder/Area Name]

### Changes Summary
- Summary of changes

### Commit Messages

**English (actual commit):**
type(scope): short description

- Detail 1
- Detail 2

Closes #N

**Korean (reference):**
type(scope): short description in Korean

- Detail 1 in Korean
- Detail 2 in Korean

Closes #N
```

## Guidelines

- Keep the first line under 72 characters
- Use imperative mood in English ("add" not "added")
- Be specific about what changed and why
- Suggest separate commits for changes spanning unrelated areas
- Include scope in parentheses (e.g., `feat(yeogi):`, `fix(popup):`)
- Always include `Closes #N` or `Refs #N` when a related GitHub issue exists
- Include Co-Authored-By tag: `Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>`

## Commit Order

1. Root (common/shared changes)
2. Individual packages/projects (alphabetical or dependency order)

---

# PHASE 4: PUSH (Requires user approval)

After committing, ask the user whether to push to the remote repository.

## Workflow

1. **Present commit summary:**
   - Number of committed files, commit messages, related issue numbers
   - Current branch name and remote branch status

2. **Ask user about push:**

Use AskUserQuestion:
- header: "Push"
- question: "Push to remote repository?"
- options:
  - "Push" — Run `git push` on the current branch
  - "Don't push" — Keep local commits only, push later
  - "Push to different branch" — Enter branch name manually

3. **Execute push (if approved):**
   - Run `git push origin [branch-name]`
   - If remote branch doesn't exist, use `git push -u origin [branch-name]`
   - If push fails, diagnose cause and report to user

4. **Report results:**
   - Push successful: Provide remote URL and commit hash
   - Push skipped: Inform user they can push later with `git push`

---

# WHEN WRITING CODE

Before implementing or modifying code, always:
1. Check `errors/` for documented errors
2. Apply prevention checklists from relevant error docs
3. Reference the error doc in code comments if directly related

# FAIL-SAFE

If there are no changes to commit, inform the user that the working directory is clean.
