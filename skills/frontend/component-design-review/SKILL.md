---
name: component-design-review
description: Review or design a frontend component's boundary, API, composition model, state ownership, styling boundary, accessibility hooks, and testability. Use when a reusable React/Vue/Angular/Svelte/plain TypeScript component is proposed or causing integration pain; produces concrete component API findings and handoff recommendations, not a broad PR review.
---

# Component Design Review

## Goal

Shape one frontend component so it is understandable, composable, accessible, and testable within the project's existing conventions, with clear ownership of data, state, events, styles, and extension points.

## When to use

- Reviewing or designing a reusable modal, table, form control, picker, menu, card, or layout component.
- A component is hard to reuse because props, slots/children, events, or styling contracts are unclear.
- A component mixes presentation, fetching, routing, global state, and domain policy in a way that makes it brittle.

## When not to use

- Full PR review across many concerns; use `code-review` and hand off component API issues here.
- Accessibility-only review; use `accessibility-review`.
- State architecture across a feature or app; use `frontend-state-management`.

## Workflow

1. **Find the component's intended job.** Read the component, nearby callers, tests/stories, and the closest existing component conventions before judging the API. If visual behavior matters and you cannot run the app, Storybook, or browser, request a screenshot/story URL or exact run command and mark visual conclusions pending.
2. **Draw the boundary.** Identify what belongs inside the component versus caller-owned data, routing, persistence, domain policy, and side effects. Prefer the repo's established pattern before proposing a new abstraction.
3. **Review the public API.** Check props/inputs, events/callbacks, slots/children/render props, refs/exposed methods, default behavior, controlled vs uncontrolled state, loading/empty/error states, and naming consistency. General principle first; examples: React props/children, Vue props/emits/slots, Angular inputs/outputs/content projection, Svelte props/events/snippets.
4. **Check composition and styling.** Verify whether consumers can customize the right pieces without breaking internals; inspect CSS/module/tokens/theme boundaries, responsive behavior, and layout assumptions.
5. **Check accessibility hooks and testability.** Ensure labels, focus targets, ARIA only where needed, keyboard affordances, stable selectors, and unit/story/interaction tests are possible without reaching into private internals.
6. **Recommend minimal changes.** Separate must-fix API defects from optional polish; avoid turning one component into a design-system project unless the current use cases prove it.
7. **Write the handoff.** Before writing it, load `references/checklist.md` and verify every item; consult `references/anti-patterns.md` whenever reuse, abstraction, or visual-behavior claims feel ambiguous.

## Questions to ask (only when necessary)

- Which consumers are committed versus hypothetical, if the code does not show them.
- Whether the component must match an external design system not present in the repo.
- Visual evidence access: Storybook route, app route, screenshot, or command when needed.

## Quality bar

- Findings cite component files, callers, stories, tests, or screenshots actually inspected.
- Recommendations preserve existing project conventions unless a concrete defect justifies deviation.
- Accessibility, styling, and testability are treated as part of the component contract, not afterthoughts.

## Expected output format

```markdown
## Component review: <component>

**Intended role**: <what this component owns / does not own>
**Evidence reviewed**: <files, callers, stories/tests, visual evidence or pending request>

**Findings**
- <severity> — <API/boundary/composition issue> — evidence: <location> — recommendation: <change>

**Proposed component contract**: <props/events/slots/state ownership summary>
**Verification**: <tests/stories/browser checks run or exact pending checks>
**Handoff**: <must-fix items, optional follow-ups, open evidence>
```

## Failure modes to avoid

- **Hypothetical reuse tax**: adding generic extension points for consumers that do not exist.
- **Visual guessing**: asserting layout or interaction quality from code alone when screenshots, Storybook, or DOM evidence are required.
- **Framework tunnel vision**: prescribing framework-specific patterns before checking the project's own conventions.

More in `references/anti-patterns.md`.
