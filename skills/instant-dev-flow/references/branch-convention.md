# Branch Naming Convention

> Reference: [Conventional Branch](https://conventional-branch.github.io/)

## Format

```
<type>/<description>
```

Optionally with ticket number:
```
<type>/<ticket-id>-<description>
```

## Branch Types

| Type | Purpose | Example |
|------|---------|---------|
| `main` | Primary production branch | `main` |
| `develop` | Integration branch (if using gitflow) | `develop` |
| `feat/` | New feature | `feat/user-authentication` |
| `fix/` | Bug fix | `fix/login-redirect-loop` |
| `hotfix/` | Urgent production fix | `hotfix/security-patch` |
| `release/` | Release preparation | `release/v1.2.0` |
| `chore/` | Non-code tasks (deps, config) | `chore/update-dependencies` |
| `docs/` | Documentation only | `docs/api-reference` |
| `refactor/` | Code restructuring | `refactor/auth-middleware` |
| `test/` | Test additions | `test/payment-integration` |

## Naming Rules

1. **Lowercase only**: Use `a-z`, `0-9`, and hyphens (`-`)
2. **No consecutive hyphens**: `feat/new--login` → `feat/new-login`
3. **No trailing hyphens/dots**: `feat/login-` is invalid
4. **Descriptive but concise**: 2-4 words max
5. **Dots allowed** only in release branches for versions: `release/v1.2.0`

## With Ticket Numbers

```
feat/PROJ-123-add-login
fix/PROJ-456-header-overflow
```

- Ticket ID comes right after the type prefix
- Description follows after the ticket ID

## Examples

```bash
# Feature branches
feat/user-dashboard
feat/PROJ-42-payment-integration

# Bug fixes
fix/null-pointer-on-submit
fix/PROJ-99-date-parsing

# Hotfixes
hotfix/csrf-vulnerability

# Release
release/v2.0.0

# Chore
chore/upgrade-next-15
```
