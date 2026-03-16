---
name: project-structure
description: Analyze a project's directory structure, generate a visual tree with role descriptions, and suggest improvements (naming, organization, unused files). Use this skill when the user asks to review, document, organize, or clean up their project structure, folder layout, or directory tree. Also trigger when the user mentions "project structure", "directory cleanup", "folder structure", or wants to understand how their project is organized.
argument-hint: "[mode: analyze | document | improve | all (default: all)]"
---

# Project Structure Organizer

Analyze a project's directory structure, document each folder/file's role, and suggest concrete improvements — then apply changes with user approval.

## Language

Auto-detect language from the user's message. Default to English if ambiguous. Do NOT ask the user to select a language. Use the detected language consistently for all output.

---

## Step 1: Project Discovery (Parallelize All Reads)

**CRITICAL: Execute all file reads and glob scans in a SINGLE parallel tool call.** Do not read files sequentially.

In one parallel batch:
- **Glob scan**: `**/*` up to 4 levels, excluding `node_modules/`, `.next/`, `dist/`, `build/`, `__pycache__/`, `.git/`, `venv/`, `.venv/`, `target/`, `vendor/`
- **Read root configs** (in parallel): `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `pom.xml`, `tsconfig.json`, or whichever exist
- **Read project docs** (in parallel): `README.md`, `CLAUDE.md`
- **Check monorepo indicators**: `workspaces` in package.json, `lerna.json`, `turbo.json`, `nx.json`

From configs, detect:
- Project type & framework (Next.js, React, Vue, Django, Flask, FastAPI, Spring, Rails, Go, Rust, etc.)
- Monorepo structure if applicable

---

## Step 2: Structure Analysis

Evaluate against ecosystem conventions and detect issues. Do this analysis in your reasoning — do NOT make additional tool calls unless strictly necessary.

### Conventions Reference

| Ecosystem | Expected Patterns |
|-----------|------------------|
| Next.js (App Router) | `src/app/`, `src/components/`, `src/lib/`, `public/` |
| React (Vite/CRA) | `src/components/`, `src/hooks/`, `src/utils/`, `src/assets/` |
| Python | `src/<pkg>/` or `<pkg>/`, `tests/`, `docs/` |
| FastAPI/Django | `app/`, `routers/`/`views/`, `models/`, `schemas/` |
| Go | `cmd/`, `internal/`, `pkg/`, `api/` |

### Issue Checklist

Scan for: flat sprawl (>15 files in one dir), deep nesting (>5 levels), inconsistent naming (mixed casing), orphan files, duplicate concern dirs (`utils/` + `helpers/`), missing standard dirs (`tests/`, `docs/`), config clutter, empty directories.

Apply framework-specific checks when applicable (e.g., Next.js route groups, Python `__init__.py`).

---

## Step 3: Present Results (Combined Output)

Output everything in a **single response** — tree, stats, issues, and proposals together.

### 3a. Structure Tree

```
project-root/
├── src/                        # Source code
│   ├── app/                    # Next.js App Router pages
│   │   ├── layout.tsx          # Root layout
│   │   └── page.tsx            # Home page
│   ├── components/             # React components
│   │   ├── ui/                 # Reusable UI primitives
│   │   └── ... (N files)
│   └── lib/                    # Business logic & utilities
├── docs/                       # Documentation
├── public/                     # Static assets
└── package.json
```

Rules:
- Comment every directory with its purpose
- Comment notable files (entry points, configs) only
- Group with `... (N files)` when a directory has many items

### 3b. Statistics

`Directories: N | Files: N | Max depth: N | Largest: path/ (N files)`

### 3c. Issues & Improvement Proposals (merged)

If issues exist, present them as a single numbered list with actionable proposals:

```
1. [Naming] Rename `scripts/data_loader-4.py` → `scripts/data-loader.py`
   → Consistent kebab-case, remove version suffix

2. [Organization] Move `src/lib/utils.ts` → `src/lib/utils/index.ts`
   → Utils will grow; directory allows splitting by concern

3. [Cleanup] Remove empty directory `src/components/export/`
   → No files inside
```

Categories: `[Naming]`, `[Organization]`, `[Cleanup]`, `[Missing]`, `[Convention]`

Then ask which to apply: "all", specific numbers (e.g., "1, 3"), or "none".

---

## Step 4: Apply Changes

For approved changes:
1. **Check references first** — use Grep in parallel to find imports/references for all files being moved
2. Create directories, move/rename files, update imports
3. Show before/after summary

Warn before any change that might break imports or references.

---

## Step 5: Generate Documentation (only in `document` or `all` mode)

Save to `docs/project-structure.md`:

```markdown
# Project Structure

> Last updated: YYYY-MM-DD

## Overview
[1-2 sentence description]

## Technology Stack
| Area | Technology |
|------|-----------|
| ... | ... |

## Directory Structure
[Annotated tree from Step 3a]

## Key Directories
[Description of main directories and their organizational principles]

## Conventions
- Naming: [detected convention]
- [Other conventions]
```

---

## Mode Selection

| Mode | Steps | Use Case |
|------|-------|----------|
| `analyze` | 1-3 | Quick overview, no changes |
| `document` | 1-3, 5 | Generate documentation only |
| `improve` | 1-4 | Find and fix issues |
| `all` (default) | 1-5 | Full analysis + improvements + docs |

---

## Performance Rules

1. **Maximize parallelism**: All independent file reads and glob scans MUST happen in a single parallel tool call. Never read files one at a time.
2. **Minimize round trips**: Combine tree + stats + issues + proposals into one output. Do not present them in separate messages.
3. **Analyze in reasoning**: Use the conventions table and issue checklist from this prompt. Do NOT make extra tool calls to "check" conventions — analyze from the glob/read results you already have.
4. **Skip unnecessary reads**: Only read files that provide structural insight (configs, entry points). Do not read every source file.

## Safety Rules

- Never delete files without explicit user approval
- Check import references (via parallel Grep) before moving any file
- For large monorepos, analyze one package at a time
- Be practical: don't suggest restructuring working code just for textbook compliance
- Respect existing conventions — internal consistency matters more than external standards
