# Sample Task

## User prompt

"Users are seeing duplicated invoices and inconsistent payment status across services."

## Abbreviated run

- Ask which service owns invoice creation (source of truth) and which stores show the inconsistency.
- Trace the write path: invoice service writes to its DB, then emits an event consumed by the billing service.
- Obstacle: whether the event consumer is idempotent, and recent deployment history, are unknown.

## Evidence requests

- Confirmation of which service is the source of truth for invoice state.
- Event consumer code or logs showing whether duplicate event delivery is deduplicated.
- Recent deployment or incident timeline around when duplication started.

## Expected output

A diagnosis separating known facts (which stores disagree, since when) from suspected cause (event consumer not idempotent, causing duplicate invoice creation on redelivery), an affected-scope estimate marked pending until a full record query is run, and a safe reconciliation plan that reconciles from the confirmed source of truth rather than guessing.

## Why this passes the quality bar

The agent does not assume which store is correct, does not claim the affected scope is complete without a scoping query, and ties the suspected cause to the actual write/event path rather than a guess.
