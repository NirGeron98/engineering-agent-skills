# Sample Task

## User prompt

"This billing module has no tests. We need to refactor it, but first capture current behavior."

## Abbreviated run

- Inspect billing entry points, callers, fixtures, generated invoices, and any historical examples.
- Inventory current behavior for discounts, taxes, rounding, failures, and ordering.
- Obstacle: production examples are referenced in docs but not in the repo.

## Evidence requests

- Sanitized sample invoice inputs and outputs.
- `npm test -- billing` or the closest existing test command.
- Any existing golden CSV/PDF/email examples.

## Expected output

A characterization plan with safest seams, current behavior inventory, first tests, snapshot cautions, and pending examples.

## Why this passes the quality bar

The agent preserves behavior before refactoring and does not silently decide whether odd billing results are bugs.

