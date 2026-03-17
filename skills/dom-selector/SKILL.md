---
name: dom-selector
description: Apply DOM selector best practices for browser automation code
user-invokable: true
disable-model-invocation: false
---

You are writing browser automation code.

GLOBAL REQUIREMENT:

- Prefer selectors that are semantic and stable. Assume the DOM structure changes frequently.

SELECTOR PRIORITY (MUST follow in order):

1. data-testid or other data-\* attributes (e.g. [data-testid="..."])
2. Accessibility selectors: role + aria-label / accessible name
3. Stable id or name attributes (e.g. #email, #password)

FALLBACK RULES: 4) Text-based selectors are allowed only as a fallback AND must be combined with semantics

- Example: role=button + visible text
- Never rely on visible text alone if better options exist

5. CSS class selectors are discouraged
   - NEVER use hashed/generated/design-system classes (e.g. atm\_\*, c1c30bzo)
   - Only use a class selector if the class name is clearly semantic and stable
6. XPath selectors are strongly discouraged
   - Absolute XPath and index-based XPath (e.g. div[3]/button[4]) are forbidden
   - XPath may be used only as a last resort and must not rely on positional indices

ROBUSTNESS RULES (MUST):

- Avoid DOM-depth dependency and positional selection (nth-child, index selectors).
- Prefer meaning-based anchoring over structural paths.
- Use scoped queries (find an anchor first, then search within its nearest relevant container).

INTERACTION & EXTRACTION RULES (MUST):

- When interacting with UI elements, search for data-testid or accessibility attributes first.
- When extracting KPIs/metrics:
  1. Find the metric by its label text (anchor).
  2. Identify the nearest KPI card/container that owns that label.
  3. Extract the value only within that container, relative to the label.
  - NEVER extract KPI values by index (e.g. button[4]) or by absolute DOM path.

FAIL-SAFE:
If no stable selector is available, stop and explain the risk instead of generating brittle code.
