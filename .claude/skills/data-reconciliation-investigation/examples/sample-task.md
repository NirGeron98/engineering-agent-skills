# Sample task: warehouse orders total higher than the app

## Prompt

> Our BigQuery `mart.orders_summary` shows 5% more total order revenue this month than the admin panel in the app. Which one is right and why?

## Abbreviated agent run

1. Pin: A = `mart.orders_summary.total_revenue` (this month, all statuses per its SQL); B = admin panel (this month, `status = 'completed'`, from operational Postgres). First red flag: the definitions may differ on status. Confirmed with the panel owner that the panel intends "completed only" and the mart is *supposed* to match it. Delta: mart is +5.0%.
2. Reproduce: reran the mart's own query → matches the mart number (report is faithful). Reran the panel's query on Postgres → matches the panel number. So both reports are faithful to different queries.
3. Decompose by day: the +5% is *not* uniform — it's concentrated. Two days carry almost the entire delta; other days tie out to the cent.
4. Stage reconciliation (count + sum) for those two days: source `orders` and `staging.orders` agree; divergence appears at `int_orders_enriched`, which joins `orders` to `order_shipments`. On the two spike days, some orders have 2 shipments → the join fans out → revenue counted twice. Count inflated on exactly those rows.
5. Also found: the mart includes `status = 'refunded'` rows (semantic mismatch) — a smaller, constant contributor separate from the fan-out.

## Obstacle / missing evidence moment

> The agent can query BigQuery but not the operational Postgres to reproduce the panel's number.

## Exact evidence request

```
On Postgres (read replica), for this month:
  SELECT date(created_at), count(*), sum(amount)
  FROM orders WHERE status='completed' GROUP BY 1 ORDER BY 1;
Paste the rows.
```

The per-day A-vs-B comparison is marked pending until the B-side daily breakdown arrives; the fan-out proof below is from BigQuery and is not pending.

## Expected output

```markdown
## Reconciliation diagnosis: mart.orders_summary vs admin panel (monthly revenue)

**Definitions**: A = mart total_revenue (all statuses); B = panel (status='completed')
— expected equal per panel owner. Two definition gaps found (status filter + join).
**Delta**: A = $1,050,000 vs B = $1,000,000 = +$50,000 (+5.0%); shape: concentrated on
2 days (fan-out) + a small constant band (refunds)

**Stage reconciliation (count / sum)**
| stage | A-side | B-side | agree? |
|---|---|---|---|
| source orders | matches | matches | yes |
| staging.orders | matches | — | yes |
| int_orders_enriched (join order_shipments) | inflated | — | NO ← diverges |

**Mechanism**: (1) double-count via 1:many join to order_shipments on multi-shipment
orders; (2) semantic — mart includes refunded orders, panel excludes them
**Proof**:
  -- duplicated order_ids after the shipment join (the +5% spike days)
  SELECT order_id, count(*) FROM int_orders_enriched
  WHERE order_date IN ('2026-06-12','2026-06-23')
  GROUP BY 1 HAVING count(*) > 1;  -- returns the multi-shipment orders
  -- refunded rows included in mart, excluded by panel
  SELECT count(*), sum(amount) FROM mart.orders_summary WHERE status='refunded';

**Verdict**: the panel is correct; the mart is inflated. The fan-out double-counts
revenue and refunded orders shouldn't be in a "completed revenue" figure.
**Correction**: aggregate shipments to order grain before the join (or join on a
deduped shipment key); filter mart to status='completed'. Restatement: recompute
mart.orders_summary for the affected period (→ backfill-planning-and-verification)
**Standing check**: add a monthly mart-vs-source reconciliation assertion on revenue
(→ data-quality-contracts)

**Pending evidence**: B-side daily breakdown from Postgres (awaiting user)
```

## Why this passes the quality bar

Both numbers are reproduced from their own definitions and the definition mismatch is caught before debugging; the delta is decomposed by day (revealing it's concentrated, not uniform); the double-count is proven with the actual duplicated `order_id`s rather than asserted as "probably a join issue"; a clear verdict, correction, restatement scope, and standing check are given.
