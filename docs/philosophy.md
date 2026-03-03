# Philosophy

## Why This Framework Exists

AI coding assistants have fundamentally changed how software gets built. A developer can describe a feature in natural language and receive working code in seconds. The productivity gain is real. But so is the risk.

The risk is not that AI writes bad code. The risk is that AI writes code without context. Without verified requirements. Without a plan that's been reviewed. Without tests that trace back to specifications. The risk is that the speed of generation outpaces the discipline of engineering.

This framework exists because **speed without structure is not productivity — it's technical debt at machine scale.**

---

## Core Beliefs

### 1. Specifications Are Non-Negotiable

The most common failure mode in AI-assisted development is starting to code before requirements are clear. AI assistants are eager to help. They'll start generating code from a one-sentence description. And the code will work — for the scenario you described. It will fail for the ten scenarios you didn't.

A specification forces completeness. It forces you to think about edge cases, error scenarios, and constraints before a single line of code is written. It creates a contract that implementation and tests can be verified against.

**The specification is the source of truth. Not the code. Not the conversation history. The spec.**

### 2. Explicit State Prevents Drift

Software projects don't fail in one dramatic moment. They fail through gradual drift — small deviations from the plan that compound over time. A shortcut here. A "temporary" workaround there. A test that gets skipped because it's "obvious."

Explicit state tracking makes drift visible. When the workflow state says "review: not passed" and someone tries to commit, the system pushes back. When the state says "spec: approved" and the implementation deviates, the deviation is flagged.

**If the state isn't tracked, the state is unknown. Unknown state is how projects go off track.**

### 3. Sequencing Prevents Chaos

AI assistants don't have opinions about process. They'll happily write tests before the implementation exists, or refactor code that hasn't been reviewed, or commit changes that haven't been tested. They follow instructions, not workflows.

Sequential phase enforcement means:
- You can't design before planning
- You can't implement before designing
- You can't review before implementing
- You can't test before reviewing
- You can't commit before testing

This is not bureaucracy. This is engineering discipline applied to a tool that has no discipline of its own.

### 4. Review Is Where Quality Happens

Code generation is easy. Code review is where engineering happens. It's where you catch the security vulnerability the AI didn't think about. Where you notice the edge case the spec didn't cover. Where you find the architectural violation that will cause problems in six months.

Making review mandatory — and structured against the spec — ensures that generated code meets engineering standards, not just syntax requirements.

### 5. Tests Prove Compliance, Not Coverage

Test coverage metrics are misleading. 100% line coverage doesn't mean the system works correctly. It means every line was executed during testing. Those are different things.

Spec-mapped testing means every test traces back to a requirement. The question isn't "how much code is covered?" but "how many requirements are verified?" If a requirement has no test, it's not verified. If a test has no requirement, it might not be needed.

---

## What This Framework Is Not

- **Not a replacement for engineering judgment.** It's a structure that makes judgment more effective.
- **Not a guarantee of correct software.** It's a process that catches more errors earlier.
- **Not an anti-AI position.** It's pro-AI and pro-discipline simultaneously.
- **Not a heavyweight methodology.** Six phases, clear documents, measurable outcomes. Nothing more.

---

## The Fundamental Tradeoff

This framework trades some speed for significant reliability. Each phase adds time. But each phase also prevents a category of problems that would cost more time to fix later.

The question is not "can we go faster by skipping phases?" The answer is always yes — in the short term. The question is "what problems will we encounter that we wouldn't have if we'd followed the process?" The answer is usually "the expensive kind."

**Discipline is not the enemy of speed. Rework is.**
