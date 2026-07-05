# Sample task: fix a duplicating incremental and add a daily revenue mart

## Prompt

> Our `fct_payments` Dataform model has duplicate rows after last week's Airflow retries. Fix it, and add a daily revenue-by-country mart on top.

## Abbreviated agent run

1. Project shape: `workflow_settings.yaml` (defaultDataset `analytics`), layers `definitions/{sources,staging,marts}`, naming `stg_`/`fct_`/`dim_`, assertions inline in config.
2. Read `fct_payments.sqlx`: `type: "incremental"`, **no uniqueKey**, filter `ts > (SELECT MAX(ts) ...)` — both classic defects. Dependents via compile graph: `fct_revenue_monthly`, one Looker-consumed view.
3. Verified upstream grain: `stg_payments` unique on `payment_id` (dupe query: 0). Measured event lateness: p99.9 arrival lag 3.2h.
4. Fix: `uniqueKey: ["payment_id"]`, lookback filter `event_ts >= timestamp_sub(current_timestamp(), interval 6 hour)` (≈2× p99.9 lag), partition pruning confirmed on `event_ts` both sides via dry-run (2.1TB → 14GB per run).
5. Dedup of existing data: one-time full-refresh of `fct_payments` (cost estimated and flagged: 2.1TB), then assertions added: uniqueKey + nonNull(payment_id, amount_usd, country).
6. New mart `fct_revenue_daily_by_country`: type table (small output), grain "one row per (date, country)" with assertion, built on `ref("fct_payments")`, explicit column list.
7. Verified in dev workspace: compile clean, assertions green, double-ran the incremental window — 0 dupes; spot-checked 5 payments against staging; mart totals reconcile to `fct_payments` sum (diff 0).
8. Downstream: grepped `fct_revenue_monthly` — uses only columns that are unchanged; unaffected.

## Expected output

```markdown
## Dataform change: fct_payments (fix), fct_revenue_daily_by_country (new)

**Graph position**: marts; upstream stg_payments; dependents: fct_revenue_monthly,
vw_looker_payments (checked)
**Grain**: fct_payments — one row per payment_id; mart — one row per (date, country)
— both enforced by uniqueKey assertions

**Config**: fct_payments: incremental, partition=event_ts(day), cluster=country;
mart: table, partition=date
**Incremental contract**: uniqueKey=[payment_id]; lookback=6h (2× measured p99.9
arrival lag of 3.2h); re-run: MERGE absorbs overlap, 0 dupes (tested);
full-refresh: rebuilds from stg_payments, ~2.1TB scan — used once for dedup

**Verification**
- compile clean; assertions green (dev workspace)
- grain dupe check: 0 (was 41,203 duplicate payment_ids before fix)
- mart reconciles to fct_payments sum exactly
- dry-run bytes per incremental run: 2.1TB → 14GB (pruning verified both sides)

**Downstream impact**: fct_revenue_monthly — unaffected (columns unchanged,
grepped); vw_looker_payments — unaffected; dedup full-refresh corrected its
historical numbers (heads-up to analytics in handoff)

**Follow-ups**: alerting on the new assertions is covered by the existing
assertion-failure notification channel (verified in workflow settings)
```

## Why this passes the quality bar

The root causes (no uniqueKey, knife-edge filter) were identified from the actual model file; the lookback is sized from measured lateness, not a default; the cost improvement is proven by dry-run numbers; duplicates were fixed historically (full-refresh) and prevented structurally (assertion); dependents were checked by grep, not just by a clean compile.
