# Dataform BigQuery Development — Anti-patterns

## The hardcoded table path
**Symptom**: `FROM my-project.analytics.orders` inside a SQLX model.
**Why it fails**: invisible to the dependency graph (wrong run order), breaks dev/staging environments, dodges compile-time checking.
**Instead**: `ref('orders')` always; declarations for external sources.

## Append-flavored MERGE
**Symptom**: `type: "incremental"` with no `uniqueKey`; a retried run doubles the day's rows.
**Instead**: set uniqueKey for MERGE semantics, or write "append-only by design" with the reason in the model.

## The knife-edge watermark
**Symptom**: `when(incremental(), "ts > (SELECT MAX(ts) FROM " + self() + ")")` — events arriving late never load.
**Instead**: lookback window (`ts >= timestamp_sub(..., interval N hour)`) sized from observed lateness, absorbed by MERGE.

## Incremental in name, full-scan in billing
**Symptom**: the incremental filter is applied after a join, or wraps the partition column in a function — BigQuery scans the whole source every run.
**Instead**: filter directly on partition columns, pre-join; confirm with a dry-run's bytes estimate.

## Grain by assumption
**Symptom**: "orders is one row per order" — except cancelled-and-recreated orders share order_number; the join fans out and revenue doubles in the mart.
**Instead**: verify grain with a duplicate-check query before every join or aggregation on a new upstream.

## Assertion-free shipping
**Symptom**: correctness checked by hand at PR time; three months later a source change breaks grain and dashboards drift for weeks.
**Instead**: uniqueKey/nonNull assertions are the model's permanent contract; they run every execution.

## Compile-green complacency
**Symptom**: renamed `revenue` to `gross_revenue`, compile passed (no dependents use ref-time column checking) — dashboards break at run time; or worse, a dependent had its own `revenue` and now silently means something else.
**Instead**: grep every dependent for the touched columns; coordinate or deprecate.

## The eternal full-refresh crutch
**Symptom**: every doubt about an incremental — a dupe, a gap, a late batch — is "fixed" with `--full-refresh`, weekly, on a multi-TB table.
**Why it fails**: the full-scan cost recurs forever, and the underlying merge-contract bug (missing uniqueKey, undersized lookback) remains to corrupt the next window.
**Instead**: treat a needed full-refresh as a symptom; diagnose and fix the merge contract, and reserve full-refresh for genuine schema/logic migrations with the cost stated.

## SELECT * plumbing
**Symptom**: staging models `SELECT *` from sources; every upstream column addition ripples cost and surprise columns through the graph.
**Instead**: explicit column lists at layer boundaries — that's where the contract lives.

## View chains doing heavy lifting
**Symptom**: five stacked views recomputing a window function for every consumer query.
**Instead**: materialize as table/incremental at the point where computation gets expensive or is reused.
