# Sample task: replicate orders tables to BigQuery

## Prompt

> We need `orders` and `order_items` from our production Postgres in BigQuery, fresh within an hour. There's already an Airflow-based ingestion for `customers`.

## Abbreviated agent run

1. Profiled source (read replica): `orders` 48M rows / ~350k churn/day, PK `id` unique (verified), `updated_at` maintained by trigger (checked `\d orders` — trigger covers all writes), **hard deletes exist** (GDPR erasure job, found in cron repo). `order_items` 190M rows, immutable after insert (verified: zero updates in 30 days), deleted only via cascade from orders.
2. Existing framework: Airflow DAG `ingest_customers` uses watermark + MERGE into `landing.customers` with `_loaded_at`. Mirrored its structure.
3. Mapping written; flagged: `orders.total NUMERIC(12,4)` → BigQuery `NUMERIC` (safe), `orders.metadata JSONB` → `JSON`, `customer_email` excluded (PII; joinable via customer_id, matching the customers pipeline convention).
4. Strategy: `orders` — watermark on `updated_at` with 2h lookback (max transaction age measured at 40min) + **nightly anti-join for GDPR deletes** (CDC not available on this RDS tier). `order_items` — insert-only watermark on `id`, deletes handled by joining to orders' delete sweep.
5. Idempotency: MERGE on PK; double-ran the same window in dev — counts unchanged.
6. Reconciliation: per-day count comparison (last 7 days), freshness check `max(updated_at)` lag < 60min, both as Airflow sensors alerting to #data-alerts.
7. Backfill: partition-chunked by month; ran, reconciled (48,112,304 = 48,112,304), cut over to incremental. Deleted a test order in staging source → gone from warehouse after the sweep.

## Expected output

```markdown
## Pipeline: prod Postgres → BigQuery (orders, order_items)

**Source profile** (queried 2026-07-02, read replica)
| table | rows | PK verified | updated_at reliable | deletes | daily churn |
|---|---|---|---|---|---|
| orders | 48.1M | ✓ | ✓ (trigger, all writes) | hard (GDPR job) | ~350k |
| order_items | 190M | ✓ | immutable after insert | cascade only | ~1.2M inserts |

**Mapping**: see mapping/orders.md — notable: customer_email excluded (PII,
convention from customers pipeline); JSONB → JSON; NUMERIC precision preserved

**Incremental strategy**: orders: updated_at watermark, 2h lookback (max txn
age 40min, ×3 margin); order_items: id watermark (insert-only, verified)
**What it could miss**: raw-SQL writes bypassing the trigger — none found in
codebase search; accepted with weekly checksum as backstop
**Deletes**: nightly PK anti-join sweep (GDPR erasures); order_items via join
**Schema drift policy**: new columns auto-added + Slack notice; type changes fail

**Idempotency**: MERGE on PK — double-run verified: 0 duplicates
**Reconciliation**: 7-day windowed counts + 60min freshness sensor → #data-alerts
— verified slice: counts equal, lag 11min
**Backfill**: complete, reconciled exactly (48,112,304 rows)

**Lineage**: prod.public.orders → landing.orders → staging.stg_orders
**Downstream consumers**: none yet (new tables); customers-pipeline conventions reused
```

## Why this passes the quality bar

The hard-delete discovery came from profiling (finding the GDPR cron), not assumption — and it changed the design; `updated_at` trust was verified at the trigger level; the lookback is sized from measured transaction age; idempotency and delete propagation were demonstrated, not asserted.
