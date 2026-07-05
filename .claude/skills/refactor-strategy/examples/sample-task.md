# Sample Task

## User prompt

"We need to rename and restructure a core domain model used across API, jobs, tests, and UI without breaking behavior."

## Abbreviated run

1. Reads the domain model, API serializers, job handlers, UI references, tests, and migration files.
2. Builds a caller inventory and identifies behavior that must stay stable.
3. Plans characterization tests for API responses, job behavior, persisted data, and UI display.
4. Slices the migration into adapter introduction, internal rename, caller migration, compatibility removal, and cleanup.

## Obstacle moment

The agent cannot run tests and does not know the compatibility window.

Exact evidence requests:

```text
Please run the current API, job, and UI test suites that cover this domain model and paste failures.
Please confirm the compatibility window: can old API fields persist for one release, multiple releases, or not at all?
```

## Expected output

A safe refactor plan with characterization tests, migration slices, compatibility and rollback strategy, verification checklist, cleanup plan, and pending evidence.

## Why this passes the quality bar

The plan preserves behavior before restructuring, avoids a big-bang rename, and keeps compatibility decisions explicit.
