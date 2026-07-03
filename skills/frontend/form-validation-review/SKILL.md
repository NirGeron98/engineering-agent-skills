---
name: form-validation-review
description: Review frontend form validation, submission UX, client/server validation boundaries, async checks, and error mapping. Use when a form has confusing validation, client/server mismatches, double submits, async uniqueness checks, inaccessible errors, or bad reset/loading behavior; produces a field-by-field validation and submission review.
---

# Form Validation Review

## Goal

Make a form's validation, submission, error handling, loading states, and reset behavior correct and humane while keeping client and server responsibilities explicit.

## When to use

- Reviewing a create/edit form, wizard step, search form, or settings form.
- Client validation disagrees with server validation.
- Async uniqueness checks, server errors, disabled states, or double-submit bugs are present.

## When not to use

- General component API review; use `component-design-review`.
- Backend validation contract design beyond the form boundary; use `api-design-review`.
- Broad accessibility review outside form behavior; use `accessibility-review`.

## Workflow

**Evidence access rule:** when you cannot run the form, browser, tests, or network inspection yourself, provide the exact command/manual check/evidence request and mark runtime-dependent conclusions pending.

1. **Map the form.** List fields, defaults, required/optional rules, submission endpoint/action, success path, and reset/cancel behavior from code and existing tests.
2. **Compare client and server validation.** Inspect schemas, backend error shape, generated types, or API docs. Client validation may improve UX, but server validation remains authoritative.
3. **Trace submission UX.** Check dirty/touched state, disabled/loading behavior, double-submit prevention, optimistic or pessimistic save, retry, cancellation, and what happens on navigation away.
4. **Review errors.** Verify field-level vs form-level placement, server error mapping, async validation states, uniqueness checks, race handling, and preserving user input.
5. **Review accessibility.** Check labels, descriptions, `aria-describedby`, error announcements, focus after submit, keyboard flow, and non-color-only signals.
6. **Verify with representative paths.** Cover initial empty submit, invalid field, async validation pass/fail, server rejection, success, and reset. If unavailable, output exact checks.
7. **Write the handoff.** Before writing it, load `references/checklist.md` and verify every item; consult `references/anti-patterns.md` when tempted to trust schemas, disable too much, or hide server errors.

## Questions to ask (only when necessary)

- Business rules that are not represented in code or contract.
- Whether partial save/draft behavior is required.
- Runtime evidence access for browser/network paths.

## Quality bar

- Each validation claim maps to a field, rule, and evidence source.
- Client/server mismatches are explicit and resolved by contract or mapping changes.
- Submission cannot be double-fired silently and server errors remain visible and actionable.

## Expected output format

```markdown
## Form validation review: <form>

**Form map**: <fields, endpoint/action, success/reset behavior>
**Client/server boundary**: <schemas/contracts inspected>

**Findings**
- <severity> — <field/submission/error issue> — evidence: <location> — recommendation: <change>

**Validation matrix**
| path | expected behavior | verified/pending |

**Handoff**: <must-fix, pending checks, follow-ups>
```

## Failure modes to avoid

- **Client-authoritative validation**: treating client checks as the source of truth and ignoring server rejection paths.
- **Disabled dead end**: disabling submit without explaining what is invalid or how the user can proceed.
- **Async race acceptance**: letting an old uniqueness response overwrite a newer field value.

More in `references/anti-patterns.md`.
