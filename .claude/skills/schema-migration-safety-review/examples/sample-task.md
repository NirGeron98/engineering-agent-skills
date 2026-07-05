# Sample Task

## User prompt

"Review this migration that adds a non-null column and backfills a large live table."

## Abbreviated run

- Read the migration diff: adds column, sets default, backfills existing rows, then adds NOT NULL constraint.
- Flag that adding NOT NULL directly can lock the table on some engines depending on version.
- Obstacle: production row count and database engine/version are not yet known.

## Evidence requests

- Approximate row count of the target table in production.
- Database engine and version in use.
- Whether the backfill is batched or a single statement.

## Expected output

A migration safety review noting lock/downtime risk depends on engine/version and row count (marked pending), a safe rollout plan recommending a three-step pattern (add nullable column → batched backfill → add constraint separately), a rollback/roll-forward plan, and a validation checklist to confirm backfill completeness.

## Why this passes the quality bar

The agent does not assume the migration is safe by default, requires table size and engine version before finalizing lock-risk conclusions, and proposes a compatibility-safe rollout pattern instead of a single risky statement.
