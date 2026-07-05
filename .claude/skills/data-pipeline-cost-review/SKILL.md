---
name: data-pipeline-cost-review
description: Review a data warehouse pipeline for cost — find which jobs, models, and queries drive warehouse spend and why (full scans on partitioned data, non-incremental rebuilds, over-frequent scheduling, unpruned partitions, wide SELECTs, redundant materializations) and propose changes that cut cost without losing correctness or freshness. Use when a warehouse bill spikes or grows, or someone asks "why is BigQuery/Snowflake so expensive" or "reduce our pipeline cost". Produces a cost-attribution report with the top drivers, per-fix savings estimates, and correctness/freshness risk notes. For a single slow/expensive query in an operational DB, use query-performance-review.
---

# Data Pipeline Cost Review

## Goal

Attribute warehouse spend to the specific jobs/models/queries that cause it, using billing and query-history evidence, then propose the highest-savings changes — each with an estimated saving and an explicit note on what it costs in correctness, freshness, or maintainability. Cut the bill without quietly breaking the data.

## When to use

- The warehouse bill spiked or is trending up and someone wants to know why.
- "Why is BigQuery/Snowflake/Redshift so expensive?" / "reduce our data pipeline cost".
- Before scaling a pipeline up, to right-size it first.
- A specific model or scheduled job is suspected of over-billing.

## When not to use

- A single slow/expensive query in an operational (OLTP) database — use `query-performance-review`.
- Optimizing one Dataform/BigQuery model's SQL and incrementality in place — use `dataform-bigquery-development` (this skill finds *which* models to send there).
- Cloud infrastructure cost outside the warehouse (compute, storage, egress) — that's a DevOps/infra concern.
- Application/service performance cost — use `throughput-bottleneck-triage` or `hot-path-profiling-review`.

## Workflow

**Evidence access rule (applies to every step):** when you cannot query billing exports, the warehouse's query/job history (e.g. `INFORMATION_SCHEMA.JOBS`, Snowflake `QUERY_HISTORY`, `ACCOUNT_USAGE`), or run a dry-run yourself, output the exact query for the user to run and paste back. Mark any spend attribution or saving estimate that depends on missing data as *pending*. Never estimate savings from a guess about what a job scans — measure it.

1. **Get the actual cost distribution first — don't guess the culprit.** Query job/query history for the period: bytes scanned/slot-seconds/credits by job, model, user, and schedule. Rank by total cost (frequency × per-run cost), not per-run cost alone — a cheap query run every 5 minutes can outspend a daily monster. The top ~10 usually explain most of the bill (Pareto).
2. **Attribute each top driver to a cause.** For each expensive job, read its SQL and config and identify the mechanism:
   - **Full scan of partitioned/clustered data**: no partition filter, or a filter that doesn't prune (function on the partition column, dynamic filter defeating pruning).
   - **Non-incremental rebuild**: `CREATE TABLE AS` over full history nightly when only recent data changed.
   - **Over-frequent scheduling**: hourly refresh of data that changes daily; a job whose freshness SLA doesn't justify its cadence.
   - **Wide reads**: `SELECT *` on wide/nested tables scanning columns nobody uses.
   - **Redundant materialization / recompute**: the same expensive CTE recomputed in many models; large intermediates rematerialized when a view would do (or vice versa).
   - **Skew / large joins / exploding intermediates**: a fan-out that inflates rows scanned downstream.
3. **Size each fix's saving from evidence.** For the proposed change, estimate the new cost (dry-run the rewritten query, or compute bytes after partition pruning / incremental scoping) and the saving = current − projected, × its run frequency. A saving without a number is a hunch.
4. **State the correctness/freshness/maintenance cost of each fix.** Incremental instead of full rebuild → adds late-data and idempotency risk (does the incremental logic handle it?). Lower cadence → more staleness (still within SLA?). Dropping columns → check consumers. Cost cuts that break data or violate a freshness contract are not savings. Cross-check freshness against `data-quality-contracts` expectations.
5. **Check the cheap structural wins.** Partitioning/clustering the largest scanned tables; required partition filters; result caching / not disabling it; materializing a hot repeated CTE once; killing zombie jobs and unused models (verify they're truly unused via lineage/query history before deleting).
6. **Prioritize by saving vs. risk/effort.** Rank fixes: big saving + low risk first (usually partition filters and cadence cuts); flag anything that needs careful incremental-logic work as higher-effort. Note total addressable saving.
7. **Hand off** the report per the output format; route in-place model rewrites to `dataform-bigquery-development` and nontrivial recomputes to `backfill-planning-and-verification`. Before finishing, load `references/checklist.md`; consult `references/anti-patterns.md` when estimating savings or risk.

## Questions to ask (only when necessary)

- The freshness SLA a job must meet, when proposing a cadence reduction and no contract states it.
- The billing model in effect (on-demand bytes vs flat-rate slots / credits), if it changes what "cost" means.
- Whether a suspected-unused model has consumers the query history can't see (external tools).

The cost distribution and per-fix savings are measured from history/dry-runs, never assumed.

## Quality bar

- Top cost drivers ranked from actual job/query history (frequency × per-run), not intuition.
- Each driver attributed to a specific mechanism found in its SQL/config.
- Each fix carries an evidence-based saving estimate (dry-run or bytes-after-pruning).
- Each fix carries an explicit correctness/freshness/maintenance risk note.
- "Unused" models verified against lineage/query history before deletion is proposed.
- Fixes prioritized by saving vs risk/effort, with a total addressable-saving figure.

## Expected output format

```markdown
## Data pipeline cost review: <warehouse / scope> (<period>)

**Total spend (period)**: <amount / bytes / credits> — top 10 jobs = <% of total>
**Billing model**: <on-demand bytes / flat-rate slots / credits>

**Top cost drivers**
| rank | job/model | cost (period) | freq | mechanism |
|---|---|---|---|---|
| 1 | mart.x rebuild | $X / N TB | nightly full | non-incremental rebuild |

**Recommended fixes (by saving vs risk)**
1. <fix> — est. saving: <current → projected, method> — risk: <freshness/correctness note> — effort: <low/med/high> (<evidence>)

**Cheap structural wins**
- <partitioning / required filter / caching / dead-model removal> (<evidence>)

**Total addressable saving**: <sum + confidence>
**Route to**: <models needing in-place rewrite → dataform-bigquery-development; recomputes → backfill-planning-and-verification>
**Pending evidence**: <history/dry-run queries awaiting user, if any>
```

## Failure modes to avoid

- **Guessing the culprit**: optimizing the query that *looks* expensive instead of the one billing history proves is expensive — the real driver is often a cheap query on a punishing schedule.
- **Per-run tunnel vision**: chasing a big nightly job while a 5-minute cron quietly outspends it over the month; always multiply by frequency.
- **Cost cuts that break data**: switching a full rebuild to incremental without handling late data/idempotency, or cutting cadence below the freshness SLA — that's not a saving, it's a future incident. Always state the correctness/freshness cost.

More in `references/anti-patterns.md`.
