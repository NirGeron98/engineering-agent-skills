# Sample Task

## User prompt

"Review our OpenAPI docs for the invitations endpoints before publishing them."

## Abbreviated run

- Inspect OpenAPI file, invitation routes, request/response schemas, auth guard, tests, and examples.
- Find that docs show a 200 response but implementation returns 202 for bulk create.
- Obstacle: generated SDK examples are not in the repo.

## Evidence requests

- Generated SDK example output for the invitation endpoints.
- `npm test -- invitations`
- Command used to regenerate OpenAPI docs.

## Expected output

An API docs review with mismatch report, missing error/auth docs, corrected examples, and design handoffs if contract issues appear.

## Why this passes the quality bar

Every mismatch ties docs to implementation evidence and does not redesign the endpoint inside a docs review.

