# DB-to-DWH Pipeline — Anti-patterns

## Profiling prod at rush hour
**Symptom**: `SELECT COUNT(*)`, checksums, and churn queries fired at the production primary at 11am because that's where the credentials pointed.
**Why it fails**: the profiling pass meant to de-risk the pipeline becomes its first incident — locks, IO pressure, replication lag.
**Instead**: profile the read replica; if only the primary exists, run off-peak with bounded, indexed queries — and say in the profile where and when it ran.

## The trusted timestamp
**Symptom**: incremental on `updated_at`, but the nightly bulk-repair script uses raw SQL that never touches it — those rows never sync again.
**Instead**: enumerate write paths (ORM hooks, triggers, scripts) and sample for rows where `updated_at` < a known change; distrust until verified.

## Deletes? What deletes?
**Symptom**: source hard-deletes cancelled orders; warehouse keeps them forever; revenue dashboards over-report and nobody knows why.
**Instead**: determine delete behavior during profiling; propagate via CDC/snapshot-diff/anti-join, or document non-propagation as an accepted, visible decision.

## Append-and-pray loading
**Symptom**: retry after a failure doubles yesterday's rows; dedup becomes a permanent downstream chore.
**Instead**: MERGE by primary key or replace by partition; prove idempotency with a deliberate double-run before shipping.

## The knife-edge watermark
**Symptom**: `updated_at > :last_watermark` exactly; a transaction open during the last run commits with an older timestamp; its rows are gone.
**Instead**: overlap a lookback window (sized from max transaction duration) and rely on idempotent MERGE to absorb the overlap.

## Timezone roulette
**Symptom**: naive timestamps loaded as UTC when the source writes local time; every time-based join downstream is off by hours.
**Instead**: determine source timezone semantics during profiling; convert explicitly; flag it in the mapping.

## Silent schema drift
**Symptom**: source adds a column; the loader keeps working and silently never captures it — discovered a quarter later.
**Instead**: explicit drift policy — auto-add with notification, or fail loudly. Never silence.

## Reconciliation as a someday-task
**Symptom**: "we'll add data quality checks after launch" — drift discovered by an analyst in a business review.
**Instead**: count + freshness checks ship in the same PR as the pipeline; they're part of the definition of done.

## CDC for a lookup table
**Symptom**: log-based CDC infrastructure stood up for a 5k-row `countries` table.
**Instead**: match strategy to volume and churn; nightly full reload is a proud, correct choice for small tables.
