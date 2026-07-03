# Sample task: contract for a subscriptions fact table

## Prompt

> Finance found duplicated subscriptions in `fct_subscriptions` last month and nobody noticed for three weeks. Put proper data quality guarantees on that table.

## Abbreviated agent run

1. Consumers found: finance MRR dashboard (Looker usage: `subscription_id`, `mrr_usd`, `status`, `valid_from`), churn model featurestore job (greps to a repo reading `status`, `plan_id`, `canceled_at`), weekly board export.
2. Profiled 90 days: ~2.1M rows, weekday inserts 6–9k / weekend 1–2k; `plan_id` null 0.4% stable (legacy rows, pre-2023); duplicate `subscription_id` present **today** (the June incident was never fully cleaned — reported as a finding); arrival p99 by 05:40 UTC daily; `status` domain: 5 values + one typo'd value `"cancelled "` (trailing space) in 12 rows.
3. Business rules from finance: `mrr_usd >= 0`; `canceled_at` only when status = canceled; one active subscription per (customer_id, plan_id).
4. Contract drafted; thresholds calibrated (volume band per weekday ±3σ; plan_id null ≤ 0.6%, not zero — legacy reality); freshness: complete by 06:30 UTC (p99 + 50min margin; dashboard consumed at 09:00).
5. Implemented as Dataform assertions (repo's existing framework) + one custom SQL assertion for the active-uniqueness rule; contract doc at `contracts/fct_subscriptions.md`.
6. Backtest over 90 days: grain check fires on the June incident window (catches it on day one — 20 days faster than humans did) and on the residual dupes; volume check fired twice on false alarms (holiday Mondays) → recalibrated with holiday awareness; freshness fired once on a real Airflow outage.
7. Breach policies: grain + schema → block; status-domain and business rules → quarantine to `dq_review.fct_subscriptions_rejects`; volume/freshness → alert to #data-alerts, owner data-eng on-call. Forced a grain failure in dev → alert arrived with runbook link.

## Expected output

```markdown
## Data quality contract: analytics.fct_subscriptions

**Consumers**: finance MRR dashboard (subscription_id, mrr_usd, status,
valid_from — Looker usage log); churn featurestore (status, plan_id,
canceled_at — ml-features repo); weekly board export
**Grain**: one row per subscription_id

**Contract**
| dimension | expectation | threshold (evidence) | breach policy | owner/alert |
|---|---|---|---|---|
| grain | subscription_id unique | 0 dupes (incident 2026-06) | block | data-eng on-call |
| schema | column removals/renames reviewed | additive free | block | PR review |
| completeness | plan_id null rate | ≤0.6% (stable 0.4% legacy) | alert | #data-alerts |
| completeness | status in 5-value domain | 0 outside (typo rows found) | quarantine | #data-alerts |
| freshness | complete by 06:30 UTC | p99 05:40 + margin; dash at 09:00 | alert | on-call |
| volume | inserts in weekday band | ±3σ per weekday, holiday-aware | alert | #data-alerts |
| business | mrr_usd ≥ 0; canceled_at ⇔ status=canceled; one active per (customer, plan) | finance-confirmed | quarantine | finance-data |

**Implementation**: Dataform assertions + custom SQL assertion
(definitions/marts/fct_subscriptions.sqlx, definitions/assertions/), contract at
contracts/fct_subscriptions.md
**Backtest**: 90 days — caught the June incident on day 1 (humans took 21);
2 false volume alarms → holiday calendar added; 1 real freshness catch
**Alert path test**: forced grain failure in dev 2026-07-02 → #data-alerts with
runbook link: yes

**Schema change policy**: additive columns free; removals/renames/meaning
changes require contract PR + consumer sign-off

**Known gaps**: currency conversion accuracy of mrr_usd excluded — owned by the
fx pipeline's own contract
**Findings along the way**: 214 residual duplicate rows from June + 12 typo'd
status rows exist today — cleanup task handed to pipeline owner
```

## Why this passes the quality bar

Every check maps to a named consumer or a finance-stated rule; thresholds carry their calibration evidence (including the deliberately non-zero null tolerance); the backtest proves the grain check would have caught the real incident 20 days earlier; breach policies differentiate block/quarantine/alert; and profiling surfaced live defects (residual dupes, typo'd statuses) that were reported rather than silently absorbed into thresholds.
