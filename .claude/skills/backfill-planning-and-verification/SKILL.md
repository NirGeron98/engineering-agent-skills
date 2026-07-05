---
name: backfill-planning-and-verification
description: Plan and verify a data backfill — recomputing or reloading historical rows/partitions after a logic fix, new column, late data, or corrected source — safely, idempotently, and without corrupting good data or blowing the budget. Use when asked to "backfill", "recompute history", "restate the numbers", or reload a range after fixing a bug. Produces a backfill plan with scope, batching, idempotency proof, cost/load estimate, a validation strategy, and a rollback path. For designing the ongoing incremental pipeline itself, use dataform-bigquery-development or db-to-dwh-pipeline.
---

# Backfill Planning & Verification

## Goal

Turn "recompute the history" into a controlled operation: the smallest correct scope, an idempotent and re-runnable execution that can't double-count or clobber unaffected rows, a cost/load estimate made *before* running, validation that proves the backfill actually fixed what it targeted, and a way back if it goes wrong.

## When to use

- A transformation/logic bug was fixed and historical outputs must be recomputed.
- A new column/metric must be populated for existing history.
- Late-arriving or corrected source data needs to be reprocessed for a past range.
- A reconciliation or freshness fix requires restating a period.

## When not to use

- Designing or repairing the ongoing incremental model/pipeline (the forward path) — use `dataform-bigquery-development` or `db-to-dwh-pipeline`.
- Diagnosing *why* data was wrong or stale in the first place — use `data-reconciliation-investigation` or `data-freshness-investigation` first, then plan the backfill.
- An operational-DB schema migration/backfill on live OLTP tables — use `schema-migration-safety-review`.

## Workflow

**Evidence access rule (applies to every step):** when you cannot profile the target, estimate bytes scanned, or run the backfill yourself, output the exact SQL/command (including dry-run/`EXPLAIN`/cost-estimate calls) for the user to run and paste back. Mark cost, scope, and verification claims that depend on missing results as *pending*. Never run or bless a backfill on an assumed scope.

1. **Define the scope precisely — and minimally.** Which rows/partitions are actually wrong? Bound it by the change: the date range the bug spans, the specific partitions, the affected tenants/keys. Backfilling more than necessary multiplies cost and risk. Prove the scope with a query that counts the affected rows.
2. **Confirm the fix is correct on a sample before touching history.** Recompute a few affected partitions into a scratch/temp location with the fixed logic and diff against both the (wrong) current values and an independently-correct reference. Backfilling a still-wrong transformation just rewrites bad data at scale.
3. **Design idempotent, re-runnable execution.** The backfill *will* be interrupted or re-run. Use partition-replace / `MERGE` on key / delete-and-reinsert-by-range — never blind append. Each batch must be safe to run twice. State the exact write mechanism and why it can't double-count.
4. **Batch to bound blast radius and cost.** Chunk by partition/date/key range rather than one monster statement: smaller failure domain, checkpointable progress, bounded per-batch cost, and it won't lock or saturate the warehouse. Define batch size, ordering, and how progress is tracked/resumed.
5. **Estimate cost and load before running.** Bytes scanned/written per batch × batches (dry-run/`EXPLAIN`), total spend, runtime, and contention with production jobs. Pick a low-traffic window if load is significant. A full-history rewrite can out-cost months of the normal pipeline — know the number first.
6. **Isolate from live consumers.** Decide the exposure model: backfill into a shadow/staging table and swap, or write in place with the understanding that consumers see intermediate state. Avoid partially-backfilled data silently feeding dashboards. Coordinate/notify downstream owners.
7. **Define validation that proves success — before running.** Row counts pre/post per batch; the target metric now matches the reference it was failing (reconciliation); no unaffected partitions changed (checksum/count the out-of-scope rows stay identical); spot-check sampled rows. Validation runs per batch and at the end.
8. **Plan rollback / recovery.** Snapshot or retain the pre-backfill state (table clone/snapshot, or the ability to reverse the range) so a bad backfill is recoverable. State exactly how to undo.
9. **Hand off** the plan (or the executed result with evidence) per the output format. Before finishing, load `references/checklist.md`; consult `references/anti-patterns.md` when sizing scope, batches, or the write mechanism.

## Questions to ask (only when necessary)

- The authoritative reference the backfilled values must match, if not obvious.
- The acceptable cost/runtime budget and low-traffic windows, when load is material.
- Whether downstream consumers can tolerate in-place intermediate state or need a shadow-table swap.

The affected scope and per-batch cost are measured with queries, never assumed.

## Quality bar

- Scope bounded to the actually-affected rows/partitions, proven with a count query — not "reload everything to be safe".
- Fixed logic validated on a sample against an independent reference before the full run.
- Write mechanism is idempotent and re-runnable; the double-run safety is stated (and ideally shown).
- Cost/load estimated before execution, with a chosen window if significant.
- Validation defined up front: target metric fixed, unaffected partitions provably unchanged.
- A concrete rollback/recovery path exists (snapshot/clone/reversible range).

## Expected output format

```markdown
## Backfill plan: <target table> — <reason>

**Scope**: <partitions/date range/keys> — affected rows: <count> (query: <...>)
**Fix validated on sample**: <partitions tested; diff vs reference: matches>

**Execution**
- Write mechanism: <partition-replace / MERGE on key / delete+reinsert> — idempotent because <...>
- Batching: <chunk by X, size N, ordered by Y> — progress tracked via <...>
- Isolation: <shadow table + swap / in-place + consumer note>

**Cost/load estimate**: <bytes/$/runtime per batch × N> — window: <when> [pending dry-run if not run]

**Validation** (per batch + final)
- Counts pre/post; target metric now matches <reference>; out-of-scope partitions unchanged (checksum)

**Rollback**: <snapshot/clone/reverse mechanism + exact undo steps>

**Downstream**: <consumers notified/affected; exposure during run>
**Pending evidence**: <dry-runs/queries awaiting user, if any>
```

## Failure modes to avoid

- **Blind append backfill**: re-inserting the range without replacing it, so a re-run (or the original rows) double-counts — always use partition-replace/MERGE and prove the double-run is safe.
- **Backfilling still-broken logic**: rewriting history with a fix that was never validated against a reference — you've now corrupted the past at scale. Validate on a sample first.
- **No cost estimate**: launching a full-history rewrite that scans terabytes and either bankrupts the query budget or contends with production; dry-run the cost before committing.

More in `references/anti-patterns.md`.
