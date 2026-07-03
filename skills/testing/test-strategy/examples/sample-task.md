# Sample Task

## User prompt

"We are adding CSV export to reports. Plan the tests before we implement it."

## Abbreviated run

- Inspect report routes, export helpers, existing download tests, API contract, job/queue behavior, and CI test commands.
- Identify risks: permissions, filters, large exports, async job completion, file format, retry/idempotency, and browser download behavior.
- Obstacle: CI timing and existing E2E artifact support are unknown.

## Evidence requests

- `npm test -- --runInBand reports`
- `npm run test:e2e -- reports-export`
- CI runtime or previous job timing for browser download tests.
- Existing export examples or fixture files if not present in repo.

## Expected output

A test strategy with a risk matrix, unit tests for format/filter logic, integration tests for API/job/storage behavior, one E2E happy path for download, manual checks for large files if needed, and tests explicitly deferred. For example: a dedicated unit test for CSV field-escaping is deferred because the existing CSV library already has escaping coverage upstream, so the added test would duplicate library-level verification without covering new risk.

## Why this passes the quality bar

The agent chooses layers from risk and evidence, does not chase coverage percentage, and marks CI/runtime conclusions pending until evidence exists.

