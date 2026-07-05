# Sample task — Form Validation Review

## Prompt

"Review the team invite form. Users say emails pass client validation but fail on submit, and the name uniqueness check flickers."

## Abbreviated run

1. Opened invite form, validation schema, submit action, backend error type, and tests.
2. Compared client email rule with server parser; found client accepts spaces that server rejects.
3. Traced uniqueness check; no stale-response guard.
4. Could not run browser; requested manual checks for invalid email, duplicate name, server rejection, and double submit.
5. Loaded `references/checklist.md` before handoff.

## Expected output

```markdown
## Form validation review: team invite

**Findings**
- High — email client rule trims only on submit, so visible field can pass then fail server-side — align normalization before validation and display normalized error.
- High — uniqueness response is not keyed to the current value — ignore stale responses or cancel previous request.
- Medium — server `EMAIL_BLOCKED` maps to a toast, not the email field — map known field errors near controls.

**Validation matrix**: invalid email, duplicate name, server blocked email, double submit, and success reset pending browser verification.
```
