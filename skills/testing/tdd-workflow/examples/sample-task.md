# Sample Task

## User prompt

"Fix invoice totals for orders near midnight UTC, but do it test-first."

## Abbreviated run

- Inspect invoice total calculation and existing invoice tests.
- Add a focused test for an order created near midnight UTC.
- Obstacle: the agent cannot run the test runner in this environment.

## Evidence requests

- `npm test -- invoice-totals`
- Or the repository-specific focused command from package/CI scripts.

## Expected output

A TDD plan with the first failing test, expected red assertion, minimal fix step, refactor note after green, and verification summary.

## Why this passes the quality bar

The implementation is gated on observing or requesting the red result and does not test private date helpers directly.

