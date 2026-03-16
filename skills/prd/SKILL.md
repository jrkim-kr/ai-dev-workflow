---
name: prd
description: Design comprehensive, production-grade Product Requirements Documents (PRDs) that bridge business vision and technical execution. Use when starting a new product/feature, translating ideas into specs, or defining requirements.
user-invocable: true
argument-hint: "[topic (optional)]"
---

Design comprehensive, production-grade Product Requirements Documents (PRDs) that bridge the gap between business vision and technical execution. This skill works for modern software systems, ensuring that requirements are clearly defined.

> **Note:** When used within `/instant-dev-flow`, the PRD output is saved to `docs/mvp-prd.md`.

## When to Use

- Starting a new product or feature development cycle
- Translating a vague idea into a concrete technical specification
- Defining requirements for AI-powered features
- Stakeholders need a unified "source of truth" for project scope
- User asks to "write a PRD", "document requirements", or "plan a feature"

## Operational Workflow

### Phase 1: Discovery (The Interview)

Before writing a single line of the PRD, you MUST interrogate the user to fill knowledge gaps. Do not assume context.

Use AskUserQuestion (up to 3 rounds, max 4 questions each):

**Round 1 — Problem & Users:**
- What specific problem does this solve? Who experiences it?
- Who are the target users? (roles, personas)
- What does success look like? (measurable outcomes)
- Are there existing solutions? What's wrong with them?

**Round 2 — Scope & Constraints:**
- What are the must-have features for MVP?
- What are explicit non-goals? (features to exclude)
- Timeline or deadline constraints?
- Budget or resource constraints?

**Round 3 — Technical & UX (dynamic based on Round 1-2):**
- Any preferred tech stack or existing infrastructure?
- Key user flows to support?
- Integration requirements? (auth, payments, external APIs)
- Design preferences or brand guidelines?

### Phase 2: Analysis & Scoping

Synthesize the user's input. Identify dependencies and hidden complexities.
- Map out the User Flow
- Define Non-Goals to protect the timeline

### Phase 3: Technical Drafting

Generate the document using the Strict PRD Schema below.

## PRD Quality Standards

Requirements Quality: Use concrete, measurable criteria. Avoid "fast", "easy", or "intuitive".

## Strict PRD Schema

You MUST follow this exact structure for the output:

### 1. Executive Summary
- **Problem Statement**: 1-2 sentences on the pain point
- **Proposed Solution**: 1-2 sentences on the approach
- **Success Criteria**: 3-5 measurable KPIs

### 2. User Experience & Functionality
- **User Personas**: Table with Persona, Role, Goals, Pain Points
- **User Stories**: As a [persona], I want to [action] so that [benefit]
- **Acceptance Criteria**: Concrete, testable criteria with checkboxes
- **Non-Goals (Out of MVP Scope)**: Features explicitly excluded and why

### 3. Functional Requirements
- **Core Features (MVP)**: Table with #, Feature, Description, Priority
- **Feature Details**: Per feature — Description, User Flow, Acceptance Criteria, Edge Cases

### 4. Non-Functional Requirements
| Category | Requirement |
|----------|-------------|
| Performance | ... |
| Security | ... |
| Accessibility | ... |
| Responsive | ... |
| SEO | ... |

### 5. Technical Constraints & Assumptions
- **Constraints**: Tech stack, infrastructure, or resource constraints
- **Assumptions**: Assumptions made during scoping
- **Dependencies**: External dependencies

### 6. Risks & Mitigations
| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|

### 7. Phased Rollout
| Phase | Scope | Timeline |
|-------|-------|----------|
| MVP | [Core features] | [Target] |
| v1.1 | [Enhancements] | [Target] |
| v2.0 | [Expansion] | [Target] |

### AI System Requirements (If Applicable)
- **Tool Requirements**: What tools and APIs are needed?
- **Evaluation Strategy**: How to measure output quality and accuracy

## Implementation Guidelines

### DO (Always):
- **Ask first**: Use AskUserQuestion for structured discovery (3 rounds)
- **Define Testing**: For AI systems, specify how to test and validate output quality
- **Iterate**: Present a draft and ask for feedback on specific sections

### DON'T (Avoid):
- **Skip Discovery**: Never write a PRD without completing at least Round 1 of questions
- **Hallucinate Constraints**: If the user didn't specify a tech stack, ask or label it as TBD
- **Use vague criteria**: "Fast" → "Page load < 2s"; "Easy to use" → "Complete core flow in < 3 clicks"

## Example: Intelligent Search System

1. **Executive Summary**
   - Problem: Users struggle to find documentation snippets in massive repositories
   - Solution: An intelligent search system providing direct answers with source citations
   - Success: Reduce search time by 50%; Citation accuracy >= 95%

2. **User Stories**
   - As a developer, I want to ask natural language questions so I don't have to guess keywords
   - Acceptance Criteria: Supports multi-turn clarification; Returns code blocks with "Copy" button

3. **AI System Architecture**
   - Tools Required: `codesearch`, `grep`, `webfetch`

4. **Evaluation**
   - Benchmark: Test with 50 common developer questions
   - Pass Rate: 90% must match expected citations
