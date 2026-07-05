# Sample task: backfill a tax column after a rounding fix

## Prompt

> We fixed a rounding bug in how `tax_amount` is computed in `mart.order_lines`. It affected orders since the tax-logic change in March. Can you plan the backfill? It's a partitioned BigQuery table, partitioned by `order_date`.

## Abbreviated agent run

1. Scope: the bug was introduced 2026-03-04 (from `git log` on the transform). Proposed counting affected rows: partitions `order_date >= '2026-03-04'`. Also checked whether *every* row in range is wrong or only some (only lines where `tax_rate` has >2 decimal places) — narrowing scope and cost.
2. Validate the fix: recompute 3 sample partitions with fixed logic into `scratch.order_lines_fix`; diff against current (shows the rounding delta) and against a hand-computed reference for 20 rows → matches reference. Fix confirmed before any bulk run.
3. Idempotency: BigQuery partitioned table → use per-partition replace (`WRITE_TRUNCATE` with `$partition` decorator, or `MERGE` on `order_line_id`). Each partition rewrite is idempotent; a re-run reproduces the same partition. Blind append explicitly rejected.
4. Batching: one batch per `order_date` partition, oldest → newest, ~120 partitions; progress tracked in a small control table so an interruption resumes.
5. Cost: proposed a dry-run to size bytes scanned per partition before running the full range.
6. Isolation: `mart.order_lines` feeds finance dashboards → backfill into a shadow partition set / clone and swap, rather than rewriting in place under live readers.
7. Validation: per partition — row count unchanged pre/post (backfill fixes values, not counts); `sum(tax_amount)` now matches the fixed reference; partitions before 2026-03-04 checksum-identical (out of scope, must not change).
8. Rollback: `CREATE SNAPSHOT` of `mart.order_lines` before starting; undo = restore from snapshot.

## Obstacle / missing evidence moment

> The agent can't run BigQuery dry-runs to estimate per-partition bytes, and can't confirm the affected-row count.

## Exact evidence request

```
1) Affected-row count:
   SELECT count(*) FROM mart.order_lines
   WHERE order_date >= '2026-03-04'
     AND round(tax_rate, 2) != tax_rate;   -- the buggy-rounding rows
2) Dry-run one partition (bytes):
   bq query --dry_run --use_legacy_sql=false \
   'SELECT ... recompute for order_date = "2026-03-04"'
Paste: the count and the "This query will process N bytes" line.
```

Cost estimate and final scope count are marked pending until these return.

## Expected output

```markdown
## Backfill plan: mart.order_lines — tax rounding fix

**Scope**: order_date >= 2026-03-04, lines where round(tax_rate,2) != tax_rate —
affected rows: <count pending> (query above)
**Fix validated on sample**: 3 partitions recomputed in scratch; 20-row diff vs
hand-computed reference matches; delta is the expected rounding correction

**Execution**
- Write mechanism: per-partition WRITE_TRUNCATE (or MERGE on order_line_id) — idempotent:
  each partition rewrite reproduces the same result; no append
- Batching: one batch per order_date partition, oldest→newest (~120), progress in a
  control table for resume
- Isolation: clone + swap (finance dashboards read this table) — no in-place rewrite

**Cost/load estimate**: <bytes/$ per partition × ~120 — pending dry-run>; window: off-hours

**Validation** (per batch + final)
- Row count unchanged pre/post; sum(tax_amount) matches fixed reference; partitions
  before 2026-03-04 checksum-identical (out of scope)

**Rollback**: CREATE SNAPSHOT mart.order_lines before start; undo = restore snapshot

**Downstream**: finance dashboards on mart.order_lines — notify owners; expose only the
swapped, fully-backfilled table
**Pending evidence**: affected-row count + per-partition dry-run bytes (awaiting user)
```

## Why this passes the quality bar

Scope is bounded to the actually-buggy rows and proven with a count query rather than reloading the whole table; the fix is validated against a hand-computed reference before any bulk run; the write mechanism is explicitly idempotent with blind-append rejected; cost is to be dry-run before executing; out-of-scope partitions are required to stay checksum-identical; and a snapshot-based rollback is defined.
