---
name: accessibility-review
description: Review frontend accessibility for keyboard navigation, focus management, semantic HTML, labels, ARIA, modals/menus, error announcements, contrast, reduced motion, and screen-reader behavior. Use when asked to audit an interactive UI, modal, dropdown, page, or component for a11y; requires browser/DOM/screenshot evidence for visual or interactive claims and produces prioritized accessibility findings.
---

# Accessibility Review

## Goal

Identify accessibility defects that affect real use of the UI, prove them with code, DOM, browser, screenshot, or assistive-technology evidence, and recommend minimal fixes aligned with existing project patterns.

## When to use

- Reviewing a modal, dropdown, menu, form, page, navigation, or interactive component for accessibility.
- Keyboard, focus, label, screen-reader, contrast, or reduced-motion behavior is questioned.
- A PR claims accessibility improvements and needs verification.

## When not to use

- Component API design where accessibility is only one contract concern; use `component-design-review`.
- Form validation UX specifically; use `form-validation-review`.
- Visual design review unrelated to accessibility.

## Workflow

**Evidence access rule:** if you cannot run browser checks, inspect the DOM, use a screenshot, or run automated accessibility tests, output the exact command/manual evidence request and mark visual/interactive conclusions pending. Never declare keyboard, focus, contrast, or screen-reader behavior correct from source code alone.

1. **Scope the user path.** Identify the UI, interaction path, target users, and whether the relevant DOM is visible only after state changes.
2. **Inspect semantics and names.** Check native elements first, accessible names/descriptions, labels, headings, landmarks, table structure, and ARIA only where native HTML is insufficient.
3. **Verify keyboard and focus.** Tab order, activation keys, focus trap/return, skip links, roving focus, escape behavior, disabled controls, and visible focus.
4. **Verify dynamic announcements.** Errors, loading, success, route changes, menus, dialogs, live regions, and validation messages.
5. **Check visual accessibility.** Contrast, zoom/reflow, reduced motion, non-color cues, hit targets, and text truncation using screenshot/browser evidence where needed.
6. **Use automated checks as support.** Run axe/lint/test tooling if available, but pair it with manual keyboard/DOM review for the scoped path.
7. **Write the handoff.** Before writing it, load `references/checklist.md` and verify every item; consult `references/anti-patterns.md` when ARIA, focus, or automated checks create ambiguity.

## Questions to ask (only when necessary)

- Target WCAG level or product accessibility standard if not documented.
- Browser/assistive-tech matrix if the organization has one.
- Access to route, Storybook story, screenshot, or rendered DOM when unavailable.

## Quality bar

- Every finding has a user impact, evidence source, and concrete fix.
- Interactive and visual claims are verified through DOM/browser/screenshot evidence or marked pending.
- Native HTML is preferred over ARIA unless the component pattern requires ARIA.

## Expected output format

```markdown
## Accessibility review: <UI/path>

**Scope**: <path and states reviewed>
**Evidence**: <files, DOM/browser/screenshot/tests, pending requests>

**Findings**
- <severity> — <issue> — impact: <user impact> — evidence: <location/check> — fix: <change>

**Checks run**: <keyboard/axe/lint/screen-reader/contrast/manual checks>
**Pending evidence**: <if any>
**Handoff**: <must-fix, follow-ups>
```

## Failure modes to avoid

- **ARIA first**: adding roles and attributes where native HTML would provide the behavior correctly.
- **Automated-only audit**: passing axe/lint and skipping keyboard, focus, and dynamic-state checks.
- **Source-only certainty**: declaring focus order, contrast, or screen-reader behavior correct without rendered evidence.

More in `references/anti-patterns.md`.
