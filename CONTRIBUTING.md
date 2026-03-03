# Contributing to ai-dev-workflow

This project is in active early development. We welcome contributions that improve the framework's rigor, usability, and extensibility.

## How to Contribute

### Reporting Issues

- **Workflow gaps:** Describe scenarios where the 6-phase workflow breaks down or produces poor outcomes.
- **Template improvements:** Suggest additions or modifications to document templates with concrete examples.
- **Skill bugs:** Report cases where skill behavior deviates from documented expectations.

When opening an issue, include:
1. The phase or component affected
2. What you expected to happen
3. What actually happened
4. A minimal reproduction scenario if applicable

### Proposing Changes

1. **Fork** the repository
2. **Create a branch** from `main` with a descriptive name: `improve/review-phase-checklist`, `fix/state-transition-logic`, `add/skill-code-review`
3. **Make your changes** following the conventions below
4. **Open a Pull Request** with a clear description of what changed and why

### What We're Looking For

| Area | Examples |
|------|----------|
| **Skill definitions** | New skills, improved phase validation logic, better prompts |
| **Templates** | More structured templates, additional fields, format improvements |
| **Documentation** | Clearer explanations, more examples, better diagrams |
| **Examples** | Real-world project examples showing the workflow in action |
| **Architecture** | Phase sequencing improvements, state management enhancements |

## Conventions

### File Naming

- Skills: `skills/{skill-name}.md` — lowercase, hyphen-separated
- Templates: `templates/{artifact-name}.md` — matches the artifact it produces
- Docs: `docs/{topic}.md` — lowercase, hyphen-separated
- Examples: `examples/{project-name}/` — descriptive project name

### Writing Style

- **Be specific.** Avoid vague language. "Validates spec completeness" is better than "checks things."
- **Be opinionated.** This framework has strong opinions about process. Contributions should reinforce that, not water it down.
- **Be concise.** Senior engineers don't need verbose explanations. Say what's needed, nothing more.
- **Use examples.** Abstract descriptions are less useful than concrete demonstrations.

### Skill Definitions

When contributing a new skill:

1. Define the skill's purpose in one sentence
2. Specify input requirements (what state/files must exist)
3. Specify output artifacts (what the skill produces)
4. Define validation criteria (how to know if the skill succeeded)
5. Document phase dependencies (which phases must be complete)

### Commit Messages

Follow conventional commit format:

```
type(scope): description

feat(skills): add code-review skill with SOLID violation detection
fix(templates): add missing acceptance criteria section to spec template
docs(philosophy): clarify sequential enforcement rationale
```

Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`

## Code of Conduct

Be respectful. Be constructive. Focus on making the framework better. We're all here to solve the same problem: bringing engineering discipline to AI-assisted development.

## Questions?

Open a discussion issue tagged `[question]`. We're building this in the open and want to hear from practitioners using AI coding tools in production workflows.
