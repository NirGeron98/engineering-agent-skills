# Sample task: revenue dashboard stuck two days ago

## Prompt

> Finance says the daily revenue dashboard hasn't moved since Tuesday. It's built on the `mart.daily_revenue` table in BigQuery. Can you find out why?

## Abbreviated agent run

1. Quantify: `SELECT max(revenue_date), max(_loaded_at) FROM mart.daily_revenue` → `max(revenue_date)=2026-06-30`, `max(_loaded_at)=2026-07-02 06:15` (today). So the job *is* running daily (load-time fresh) but loads no new event dates — event-time stale by 2 days. SLA: daily by 07:00.
2. Topology: lineage shows `mart.daily_revenue` ← `staging.orders` ← `raw.orders_stream` ← operational `orders` (Postgres, via Fivetran). Four stages.
3. Bisect (max event ts per stage):
   - `raw.orders_stream`: max `created_at` = 2026-06-30 → stale here too.
   - `staging.orders`: 2026-06-30 → same.
   - operational `orders`: needs a source-side check.
   Break is at or above `raw.orders_stream`.
4. Classify: Fivetran connector run history requested. If it last synced successfully but pulled 0 rows → loaded-nothing (watermark/source). If it's paused/erroring → didn't run.
5. Source check: proposed a source-side count of `orders` created since 2026-06-30 to decide between "source stopped" and "connector stuck".

## Obstacle / missing evidence moment

> The agent has warehouse read access but cannot see the Fivetran connector's run history or query the operational Postgres directly.

## Exact evidence request

```
1) Fivetran → orders connector → last 5 syncs: status + rows written. Paste the table.
2) On the operational DB (read replica):
   SELECT date(created_at), count(*) FROM orders
   WHERE created_at >= '2026-06-30' GROUP BY 1 ORDER BY 1;
   Paste the rows (or "0 rows").
```

Classification is marked pending on these two results.

## Expected output

```markdown
## Freshness diagnosis: mart.daily_revenue

**Staleness**: newest revenue_date 2026-06-30; expected 2026-07-02; lag ~2 days vs SLA (daily 07:00)
**Freshness type**: event-time stale; load-time fresh (job runs nightly, loads nothing new)

**Chain (top → bottom)**
| stage | max event ts / last run | fresh? |
|---|---|---|
| operational orders (Postgres) | pending source count | ? |
| raw.orders_stream (Fivetran) | created_at 2026-06-30 | NO ← break at/above here |
| staging.orders | 2026-06-30 | no (downstream) |
| mart.daily_revenue | 2026-06-30, loaded 07-02 | no (downstream) |

**Break stage**: raw.orders_stream (ingestion) or the source above it
**Classification**: pending — connector-stuck (loaded-nothing) vs source-stopped, decided by the two queries
**Proof**: connector sync rows + source-side hourly count around 2026-06-30

**Blast radius**: staging.orders and every mart built on it (daily_revenue, plus any
finance/exec dashboards on those) stale since 2026-06-30

**Remediation**: if connector stuck → reset/resync the connector from last good watermark;
if source stopped → escalate to the orders service owners
**Recovery**: backfill mart.daily_revenue for 06-30 → today once ingestion catches up
(nontrivial range → backfill-planning-and-verification)
**Missing guardrail**: no event-time freshness check on raw.orders_stream — add one
(→ data-quality-contracts); the load-time success masked this for 2 days

**Pending evidence**: Fivetran sync history + source-side order counts (awaiting user)
```

## Why this passes the quality bar

Staleness is quantified with real max-timestamp queries and the load-time-vs-event-time trap is caught explicitly; the break is localized by bisecting the chain rather than reading transformation SQL; the crucial classification (connector-stuck vs source-stopped) is left pending on a direct source-side query instead of being guessed from the empty warehouse table.
