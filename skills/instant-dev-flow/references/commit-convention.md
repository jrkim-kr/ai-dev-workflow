# Commit Convention

> Based on [Conventional Commits](https://www.conventionalcommits.org/)

## Format

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

## Types

| Type | Description | Triggers |
|------|-------------|----------|
| `feat` | New feature | SemVer MINOR |
| `fix` | Bug fix | SemVer PATCH |
| `docs` | Documentation only | — |
| `style` | Formatting, whitespace (no code change) | — |
| `refactor` | Code restructuring (no behavior change) | — |
| `perf` | Performance improvement | — |
| `test` | Adding/updating tests | — |
| `chore` | Build, deps, config changes | — |
| `ci` | CI/CD configuration | — |
| `revert` | Reverting a previous commit | — |

## Scope

Scope is derived from the affected area:

| File Path Pattern | Scope |
|-------------------|-------|
| `src/app/api/...` | `api` |
| `src/components/...` | `ui` |
| `src/lib/...` | `lib` |
| `src/app/(pages)/...` | Page name |
| Root config files | Omit scope |
| Multiple directories | Project-level scope |

## Rules

1. **First line ≤ 72 characters**
2. **Imperative mood**: "add feature" not "added feature"
3. **No period** at the end of the subject line
4. **Blank line** between subject and body
5. **Body** explains "what" and "why", not "how"
6. **Footer** for issue references and co-authors

## Issue References

```
Closes #123          — closes the issue on merge
Refs #123            — references without closing
Closes #123, #456    — closes multiple issues
```

## Co-Author

Always include when AI-assisted:
```
Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

## Examples

```bash
# Simple feature
feat(auth): add Google OAuth login

# Bug fix with issue reference
fix(api): handle null response from payment gateway

Closes #234

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>

# Breaking change
feat(api)!: change authentication endpoint response format

BREAKING CHANGE: /api/auth/login now returns { token, user }
instead of { accessToken, refreshToken, userData }

Closes #567

# Chore
chore: upgrade Next.js to v15.2

# Documentation
docs(readme): add deployment instructions
```

## Bilingual Commit Display

When committing via `/issue-commit`, show both:
```
English (commit): feat(auth): add Google OAuth login
Korean (reference): feat(auth): Google OAuth 로그인 추가
```

Only the English version is used for the actual commit message.
