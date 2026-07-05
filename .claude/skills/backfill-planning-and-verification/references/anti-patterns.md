# Backfill Planning & Verification — Anti-patterns

## Blind append
**Symptom**: the backfill `INSERT`s the recomputed range without removing the existing rows for that range.
**Why it fails**: the range now has both old and new rows — double-counted; a re-run after an interruption compounds it.
**Instead**: partition-replace, `MERGE` on key, or delete-then-reinsert the range; make every batch idempotent and prove a double-run is safe.

## Backfilling unvalidated logic
**Symptom**: the fix is assumed correct and run across all history immediately.
**Why it fails**: if the fix is wrong or incomplete, you've now rewritten the past at scale with new bad data — often harder to detect than the original bug.
**Instead**: recompute a sample, diff against an independent reference, and confirm correctness before the full run.

## Reload-everything scope
**Symptom**: "to be safe, let's just rebuild the whole table."
**Why it fails**: it multiplies cost and runtime, expands the blast radius to rows that were fine, and risks changing data that had no bug.
**Instead**: bound scope to the provably-affected partitions; touch nothing outside it and verify it stayed unchanged.

## No cost estimate
**Symptom**: kicking off a full-history recompute without a dry-run.
**Why it fails**: a terabyte-scanning rewrite can blow the query budget or starve production jobs; you find out from the bill or the incident.
**Instead**: dry-run/`EXPLAIN` the per-batch cost, total it, and pick a window before committing.

## One giant statement
**Symptom**: the entire backfill is a single `MERGE`/`CREATE TABLE AS` over all history.
**Why it fails**: no checkpoint, no resume, a huge lock/slot footprint, and a single failure wastes the whole run.
**Instead**: batch by partition/date; track progress so an interruption resumes instead of restarting.

## In-place with live readers
**Symptom**: overwriting the production table in place while dashboards read it.
**Why it fails**: consumers see half-backfilled numbers and make decisions on them; a mid-run failure leaves a visibly corrupt table.
**Instead**: backfill into a shadow table and swap, or coordinate a maintenance window and notify consumers.

## Validation that only checks it ran
**Symptom**: "the job completed, so the backfill worked."
**Why it fails**: completion isn't correctness; the metric may still be wrong, or unaffected partitions may have been altered.
**Instead**: prove the target metric now matches its reference and that out-of-scope data is byte-for-byte unchanged.

## No way back
**Symptom**: no snapshot taken before overwriting history.
**Instead**: clone/snapshot the pre-backfill state or keep the operation reversible so a bad run can be undone.
