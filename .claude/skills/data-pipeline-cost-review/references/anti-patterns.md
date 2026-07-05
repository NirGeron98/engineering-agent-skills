# Data Pipeline Cost Review — Anti-patterns

## Guessing the culprit
**Symptom**: the review opens the query that "looks heavy" and optimizes it, without checking billing/job history.
**Why it fails**: the biggest driver is frequently unglamorous — a small query on a punishing schedule, or an unpruned partition scan — and the eye-catching query may be cheap.
**Instead**: rank actual spend from job/query history first; optimize what the data says is expensive.

## Per-run tunnel vision
**Symptom**: prioritizing a costly nightly rebuild while ignoring an every-5-minute job.
**Why it fails**: monthly cost is per-run × frequency; the frequent cheap job can dominate the bill.
**Instead**: always multiply per-run cost by run frequency over the period.

## Incremental without late-data handling
**Symptom**: converting a full nightly rebuild to an incremental to save cost, without addressing late-arriving rows or re-run idempotency.
**Why it fails**: the pipeline gets cheaper and quietly wrong — recent rows that arrive late are never picked up, or re-runs double-count.
**Instead**: pair the incremental with a lookback window and idempotent writes; treat it as a correctness change, not just a cost change.

## Cadence below the SLA
**Symptom**: dropping a job from hourly to daily purely for savings.
**Why it fails**: if a consumer needs sub-daily freshness, the cost cut violates the freshness contract — a future incident, not a saving.
**Instead**: reduce cadence only to the point the freshness SLA allows; cross-check with data-quality-contracts.

## Deleting a "dead" model that isn't
**Symptom**: removing a model/table because it looks unused.
**Why it fails**: external BI tools, ad-hoc users, or scheduled exports the query history doesn't fully capture may depend on it.
**Instead**: verify non-use via lineage and query history over a long window, and check external consumers, before deleting.

## Savings without numbers
**Symptom**: "making this incremental will save a lot."
**Why it fails**: an unquantified saving can't be prioritized and often disappoints; the fix might cost more in maintenance than it saves.
**Instead**: dry-run/estimate the projected cost and state the saving as a number with its method.

## Ignoring partition-filter defeat
**Symptom**: the table is partitioned, so the scan is assumed pruned.
**Why it fails**: a function wrapped around the partition column, a join-driven dynamic filter, or a missing filter scans the whole table anyway.
**Instead**: confirm pruning actually happens (dry-run bytes, query plan) rather than assuming partitioning implies pruning.

## Optimizing correctness away
**Symptom**: trimming columns or rows to cut bytes without checking what consumers read.
**Instead**: verify downstream dependencies before narrowing a model's output.
