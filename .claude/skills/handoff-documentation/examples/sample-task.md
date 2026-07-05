# Sample Task

## User prompt

"I need to stop here. Write a handoff so another agent can continue the bug fix."

## Abbreviated run

- Inspect git diff, test output, notes from the diagnosis, files touched, and remaining failing test.
- Separate confirmed root cause from pending verification.
- Obstacle: the last full test suite was not run.

## Evidence requests

- `git diff --stat`
- Focused and full test command outputs if unavailable.
- Link or text of the issue/PR if relevant.

## Expected output

A handoff note with status, evidence, files touched, verification run/not run, risks, unresolved questions, and next steps.

## Why this passes the quality bar

The note preserves incomplete state honestly and gives the next person a concrete resume path.

