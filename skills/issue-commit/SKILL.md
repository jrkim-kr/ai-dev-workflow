---
name: issue-commit
description: End-to-end commit workflow — pull latest, auto-detect change type/scope, document errors (for bugs), create GitHub issues in English, generate bilingual (English + Korean) commit messages, and push. Use this skill whenever the user wants to commit changes, create an issue for their work, document an error, or run the full issue-commit-push pipeline. Trigger on phrases like "커밋해 줘", "이슈 만들어", "commit this", "create an issue and commit", "에러 문서화", or any request combining issue creation with committing.
user-invokable: true
disable-model-invocation: false
---

You handle error documentation, GitHub issue creation, and commit message generation — all in one workflow.

**Phase order: 0 → 1 (fix only) → 2 → 3 → 4. No phase may be skipped except PHASE 1.**

---

# PHASE 0: Pre-Work + Analysis

Run all git commands in a single call to minimize tool calls:

```bash
git pull && git status && git log --oneline -5
```

If conflicts or pull failure, inform user and guide resolution.

Then analyze `git diff HEAD` to infer **change type** and **scope**:

**Change type inference:**
- New files → `feat` | Only `.md` → `docs` | Formatting only → `style` | Test files → `test`
- Bug fixes (error handling, null checks) → `fix` | Restructuring → `refactor`
- Build/config/deps → `chore` | Performance → `perf`

**Scope inference from file paths:**
- `hotel-price-updater/...` → `price-updater`
- `unified_extension/popup.*` → `popup` | `unified_extension/content/...` → `content` | `unified_extension/background.*` → `background`
- Root-level → omit scope | Multiple dirs in same project → project-level scope
- If ambiguous, ask the user

Present detected type and scope to user for confirmation, then proceed.

---

# PHASE 1: ERROR DOCUMENTATION (Only for `fix` type — skip otherwise)

1. Analyze the error: root cause, reproduction steps, context
2. Auto-discover error doc directories:
   ```bash
   find . -type d -name "errors" -not -path "*/node_modules/*"
   ```
3. Create error doc in `<project>/errors/ERR-NNN-brief-description.md`:

```markdown
# [ERROR_CODE] Short Description
## Summary
## Root Cause
## Reproduction Steps
## Solution
## Prevention Checklist
## Related Files
```

**Error code ranges:** 001-099 DOM/Selector | 100-199 Network/API | 200-299 Data parsing | 300-399 Auth | 400-499 Channel-specific

If diagnosis is incomplete, mark `Status: Under Investigation`.

---

# PHASE 2: GITHUB ISSUE CREATION

Create a GitHub Issue for **all changes**. **All issues in English.**

**Title format:** `type(scope): short description` (append `[ERR-NNN]` for bugs with error docs)

**Pre-creation checks** — run in parallel in a single call:
```bash
gh issue list --search "keyword" --state open --limit 10 && gh label list | grep -q "label-name" || gh label create "label-name" --color "ededed"
```
If duplicate found, ask user whether to reference existing or create new.

**Issue creation:**
```bash
gh issue create --title "type(scope): description" --body "..." --label "label" --assignee @me
```

**Labels:** fix→`bug` | feat→`enhancement` | refactor→`refactor` | docs→`documentation` | style→`style` | test→`test` | chore→`chore` | perf→`performance`

**Body template:**
```markdown
## Summary
## Changes
## Related Files
```
For `fix`, also include Root Cause and Solution from error doc.

Record the issue number for commit references.

---

# PHASE 3: COMMIT (English commit, show both English & Korean)

## Pre-Commit: Error Doc Check (MANDATORY for all types)

Before creating any commit, check existing error docs for prevention violations:

1. Discover error doc directories:
   ```bash
   find . -type d -name "errors" -not -path "*/node_modules/*" -not -path "*/.git/*"
   ```
2. Read all error docs and review their **Prevention Checklist** sections
3. Cross-check changed files against each checklist — determine if any violation applies
4. Report results before committing:
   - List each error doc checked
   - State whether it's relevant to the current changes (with reason)
   - If a violation is found: **STOP** and fix before committing
   - If no violations: proceed with commit

PHASE 0 already analyzed the diff — reuse that analysis here. Do NOT re-run `git diff` or `git status` unless changes were made after PHASE 0.

**Group by project** if changes span multiple directories. Commit order: Root → hotel-price-updater → unified_extension.

For each group, show both messages then commit:

```
**English (commit):** type(scope): description
**Korean (reference):** type(scope): 한국어 설명
```

**Guidelines:**
- First line under 72 chars, imperative mood
- `Closes #N` or `Refs #N` for related issues
- `Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>`
- Separate commits for unrelated areas

---

# PHASE 4: PUSH + SUMMARY

**1. Execution Summary:**

```markdown
| Phase | Result |
|-------|--------|
| Pre-Work | branch: `main`, synced, type: `feat(scope)` |
| Error Doc | Skipped / Created ERR-XXX |
| Issue | #N — `type(scope): title` |
| Commit | `type(scope): message` (X files) |
| Push | (pending) |
```

**2. Ask user about push:**

Use AskUserQuestion:
- header: "Push"
- question: "Push to remote repository?"
- options: "Push" | "Don't push" | "Push to different branch"

**3. Execute (if approved):**
- `git push origin [branch]` (use `-u` if remote branch doesn't exist)
- Update summary with push result

---

# WHEN WRITING CODE

Before implementing or modifying code:
1. Check `<project>/errors/` for documented errors
2. Apply prevention checklists from relevant error docs

**Note:** Error doc checks happen at two points — (1) before writing code and (2) before committing (PHASE 3). The PHASE 3 check is the final gate to catch any missed violations.

# FAIL-SAFE

If there are no changes to commit, inform the user that the working directory is clean.
