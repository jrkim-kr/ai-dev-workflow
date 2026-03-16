# Documentation Naming Convention

## File Naming

| Rule | Example |
|------|---------|
| Lowercase kebab-case | `setup-guide-developer.md` |
| Extension: `.md` for docs | `spec.md`, `system-design.md` |
| Extension: `.sql` for migrations | `001_initial_schema.sql` |
| No spaces or special characters | `mvp-prd.md` not `MVP PRD.md` |

## Migration Files

Format: `NNN_description.sql`

```
001_initial_schema.sql
002_add_indexes.sql
003_add_rls_policies.sql
004_functions_triggers.sql
005_seed_data.sql
006_add_notifications_table.sql
```

- 3-digit zero-padded number prefix
- Underscore separator between number and description
- Lowercase snake_case for description

## Error Documentation

Format: `ERR-NNN-brief-description.md`

```
ERR-001-dom-selector-stale.md
ERR-101-api-timeout.md
ERR-201-csv-parse-failure.md
ERR-301-auth-token-expired.md
```

Error code ranges:
| Range | Category |
|-------|----------|
| 001-099 | DOM/Selector |
| 100-199 | Network/API |
| 200-299 | Data parsing |
| 300-399 | Auth |
| 400-499 | Channel-specific |

## ADR (Architecture Decision Records)

Format: `ADR-NNN` (inline in `system-design.md`)

```
ADR-001: Use Supabase for auth and database
ADR-002: Client-side state with Zustand
ADR-003: Server Components by default
```

## Document Titles

- Include project name: `# [Project Name] — Feature Specification`
- Include metadata: `> Last modified: [Date] | Version: v1.0`
- Use `---` horizontal rules between major sections

## Cross-Reference Rules

- Use consistent terminology across all documents
- Table names, status codes, and URL paths must match between spec.md, system-design.md, and checklist.md
- When referencing another document, use relative paths: `see [spec.md](../spec/spec.md)`
