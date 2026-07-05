---
name: schema-migration-safety-review
description: Review a database schema migration that touches live data, tables, columns, constraints, indexes, or backfills for lock risk, downtime risk, backfill safety, and rollback/roll-forward readiness. Use when a migration needs a safety review before rollout. Produces a migration safety review with a risk list, safe rollout plan, rollback plan, and validation checklist.
---

# Schema Migration Safety Review

## Goal

Turn a proposed schema migration into an evidence-backed rollout and rollback plan that protects live data and availability.

## When to use

- A migration adds, drops, or alters tables, columns, constraints, or indexes on a live database.
- The migration includes a backfill against production data.
- Deployment order between app version and schema version, or rollback safety, is unclear.

## When not to use

- The change is a data pipeline or warehouse schema change; use the relevant `skills/data/` workflow.
- The question is which indexes to add for query patterns, not migration mechanics; use `.claude/skills/index-strategy-review`.
- General release/rollback process (not schema-specific) is the concern; use `.claude/skills/release-and-rollback-readiness`.

## Composition and handoff rules

- Defer general release readiness to `.claude/skills/release-and-rollback-readiness`.
- Defer technical architecture decisions to `.claude/skills/architecture-decision-record`.
- Defer data warehouse/pipeline schema concerns to existing `skills/data/` workflows.

## Evidence access rule

Inspect the actual migration diff/script, target table size, current indexes/constraints, and the app's compatibility requirements across the old and new schema versions. If the migration script, data volume, or app-version compatibility is unavailable, request exact files/numbers and mark risk and rollout conclusions pending.

## Workflow

1. Read the migration diff: what tables/columns/constraints/indexes change, and whether it includes a backfill.
2. Assess lock and downtime risk given the table's size and the database engine's behavior for this operation type.
3. Check compatibility: does the migration require the old and new app versions to both work against the schema during rollout?
4. Produce a safe rollout plan (deployment order, backfill batching, monitoring) and a rollback/roll-forward plan.
5. Produce a data validation checklist to confirm backfill/migration correctness post-deploy.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to assume a clean rollback exists or skip a compatibility window.

## Questions to ask (only when necessary)

- What is the approximate row count of the affected table(s) in production?
- Will old and new application versions run simultaneously during deployment?

## Quality bar

- Lock/downtime risk is assessed against actual table size and engine behavior, not assumed safe by default.
- The rollback plan is verified as actually reversible, not asserted.
- A compatibility window between old/new app and schema versions is explicitly addressed.

## Expected output format

```markdown
## Schema migration safety review: <migration>

**Risk list**: <lock/downtime/compatibility risks>
**Safe rollout plan**: <deployment order, backfill batching, monitoring>
**Rollback/roll-forward plan**: <steps>
**Validation checklist**: <post-deploy checks>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Rollback fantasy**: assuming a rollback is safe/possible without verifying it against the actual migration.
- **Migration without compatibility window**: ignoring that old and new app code may run against the schema simultaneously.
- **Backfill-as-afterthought**: batching or locking behavior of a live backfill not assessed against table size.

More in `references/anti-patterns.md`.
