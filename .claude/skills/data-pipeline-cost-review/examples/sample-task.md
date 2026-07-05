# Sample task: BigQuery bill doubled this quarter

## Prompt

> Our BigQuery bill nearly doubled over the last quarter and nobody knows why. Can you figure out what's driving it and how to bring it down? We're on on-demand pricing.

## Abbreviated agent run

1. Cost distribution: proposed querying `INFORMATION_SCHEMA.JOBS_BY_PROJECT` for the last 90 days, summing `total_bytes_billed` grouped by `query` fingerprint / destination table and by scheduled job, ranked by total. Also grouped by day to find when the jump started.
2. Top drivers (from the returned history): (1) `mart.events_daily` rebuild — `CREATE TABLE AS` scanning the full `raw.events` history nightly (2.1 TB/run × 90) — non-incremental rebuild, and it started scanning 2× more after a new source was added in April. (2) A dashboard freshness job refreshing `mart.kpis` every 15 minutes, each scanning 180 GB — cheap-looking but 96×/day dominates the month. (3) `SELECT *` staging model on a wide nested events table pulling 40 columns when 6 are used downstream.
3. Attribution confirmed by reading each model's SQL: #1 no incremental config; #2 cron at `*/15` though the KPIs feed a daily exec dashboard; #3 `select *` in `stg_events.sqlx`.
4. Savings sizing: proposed dry-runs of the rewritten versions — incremental `events_daily` (scan last 2 days + lookback instead of all history), `kpis` at hourly instead of 15-min, `stg_events` selecting 6 columns.
5. Risk notes: #1 incremental must handle late events (add lookback + MERGE) — correctness work, route to dataform skill; #2 hourly still beats the daily-dashboard SLA — low risk; #3 confirm the 34 dropped columns have no consumers via lineage before trimming.

## Obstacle / missing evidence moment

> The agent can read the model SQL in the repo but can't run `INFORMATION_SCHEMA` queries or dry-runs against the BigQuery project.

## Exact evidence request

```
1) Top spend (last 90d):
   SELECT destination_table.table_id, count(*) runs, sum(total_bytes_billed)/1e12 tb_billed
   FROM `region-us`.INFORMATION_SCHEMA.JOBS_BY_PROJECT
   WHERE creation_time > timestamp_sub(current_timestamp(), interval 90 day)
     AND statement_type != 'SELECT' OR cache_hit = false
   GROUP BY 1 ORDER BY tb_billed DESC LIMIT 20;
2) Dry-run the proposed incremental events_daily and paste the "bytes processed" line.
Paste back both.
```

All spend attribution and saving numbers are marked pending until these return.

## Expected output

```markdown
## Data pipeline cost review: BigQuery (last 90 days) [figures pending history export]

**Total spend (period)**: <pending JOBS_BY_PROJECT> — top 10 jobs ≈ <%> of total
**Billing model**: on-demand (bytes billed)

**Top cost drivers**
| rank | job/model | cost (period) | freq | mechanism |
|---|---|---|---|---|
| 1 | mart.events_daily rebuild | ~2.1 TB/run (pending) | nightly full | non-incremental rebuild; 2× since April source add |
| 2 | mart.kpis refresh | ~180 GB/run ×96/day (pending) | every 15 min | over-frequent vs daily SLA |
| 3 | stg_events | wide scan (pending) | nightly | SELECT * (6 of 40 cols used) |

**Recommended fixes (by saving vs risk)**
1. kpis: 15-min → hourly — est. saving: ~75% of #2 (96→24 runs) — risk: none, beats daily
   dashboard SLA — effort: low (cron change)
2. stg_events: select 6 needed columns — est. saving: proportional to column bytes — risk:
   confirm 34 dropped cols unused via lineage — effort: low
3. events_daily: full rebuild → incremental (last 2d + lookback, MERGE) — est. saving:
   largest (pending dry-run) — risk: MUST handle late events + idempotency — effort: high
   → dataform-bigquery-development for the rewrite; backfill-planning-and-verification if
   history needs recompute

**Cheap structural wins**
- Ensure raw.events queries filter the partition column directly (check pruning via dry-run)

**Total addressable saving**: <sum pending dry-runs; #1 and #2 are low-risk immediate>
**Route to**: dataform-bigquery-development (events_daily incremental), backfill-planning-and-verification (any recompute)
**Pending evidence**: JOBS_BY_PROJECT top-spend export + incremental dry-run bytes (awaiting user)
```

## Why this passes the quality bar

The culprit ranking is driven by billing/job history rather than which query looks heavy, and the 15-minute KPI job is caught precisely because cost is frequency × per-run; each fix has a savings method and an explicit risk note (the incremental one is flagged as correctness work, not a free win); dropped columns require lineage confirmation before removal; and every spend number is marked pending because the agent couldn't run the history queries itself.
