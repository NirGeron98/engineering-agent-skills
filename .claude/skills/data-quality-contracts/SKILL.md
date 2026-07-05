---
name: data-quality-contracts
description: Define and implement enforceable data quality contracts for a dataset — schema, grain, nullability, freshness, volume, and business-rule expectations — wired into the pipeline with alerting and a breach policy. Use when a dataset needs guarantees ("can we trust this table?"), after a data incident, when consumers complain about silent breakage, or when adding checks to an existing pipeline (dbt tests, Dataform assertions, Great Expectations, custom SQL). Produces a contract document plus running checks — calibrated on real data so they alert on breaches, not on Tuesdays.
---

# Data Quality Contracts

## Goal

Turn implicit assumptions about a dataset into an explicit, versioned contract — enforced by automated checks that run with the pipeline, calibrated against the data's real behavior, with a defined action per breach (block, quarantine, or alert). Trust becomes checkable instead of vibes.

## When to use

- A dataset is (or is becoming) load-bearing: dashboards, ML features, finance, external delivery.
- Post-incident: bad data reached consumers silently.
- Consumers and producers disagree about what a table guarantees.
- Adding tests/assertions to an existing pipeline in any framework.

## When not to use

- Exploratory/scratch datasets nobody depends on — contracts have maintenance cost; don't tax experiments.
- Application-level input validation (that belongs in the service).
- Choosing between quality *platforms* — this skill defines and wires contracts in whatever the repo already uses.

## Workflow

**Evidence access rule (applies to every step):** when you cannot run a profiling query, read query logs, or check BI usage yourself, output the exact query or evidence request for the user to run and paste back. Mark any threshold or consumer claim that depends on missing evidence as *pending*. Never substitute an assumption for a profile you couldn't obtain.

1. **Identify consumers and their actual dependencies.** Find who reads the dataset (query logs, BI usage, lineage, grep for the table name across repos) and which columns/properties they rely on. If query logs and BI metadata are out of reach, fall back to what you can inspect — grep dashboard/BI repos for the table name, read downstream models' refs — and ask the user for a usage export for the rest; record consumer claims you couldn't verify as assumptions. The contract protects consumers; its contents come from their dependencies, not from what's easy to check.
2. **Profile the data before writing a single expectation.** Query current reality: row counts and their weekly pattern, null rates per column, distinct values of categorical columns, value ranges, actual arrival/freshness pattern, duplicate rate on the assumed key. Contracts written from imagination produce both false alarms and false confidence.
3. **Draft the contract across the six dimensions**, keeping only what consumers need:
   - **Schema**: columns, types, semantic meaning; policy for additions (usually allowed) vs. removals/renames/meaning-changes (breaking, coordinated).
   - **Grain/uniqueness**: one row per what; key columns.
   - **Completeness**: nullability per critical column; required enum values; referential integrity to dimensions.
   - **Freshness**: max lag vs. an SLA that consumers actually need (and the pipeline can meet — check its schedule).
   - **Volume**: expected row-count band, seasonally aware (weekday/weekend, month-end).
   - **Business rules**: invariants domain experts state ("refund ≤ original amount", "status transitions only forward").
4. **Calibrate thresholds from the profile.** Volume bands from observed variance (e.g. ±3σ on day-of-week-adjusted counts), freshness from observed arrival p99 plus margin, null-rate thresholds at current-plus-tolerance rather than zero if history shows a stable baseline. A check that fires weekly on normal data will be muted within a month — and then it's worse than no check.
5. **Define the breach policy per check — this is the contract's teeth.**
   - **Block**: pipeline halts, downstream doesn't build (grain violations, schema breaks — things that corrupt everything downstream).
   - **Quarantine**: bad rows diverted to a review table, good rows flow (row-level rule violations at low rates).
   - **Alert-only**: humans notified, data flows (soft degradations, volume drift).
   Every check gets one, deliberately; also name who is notified and where. The contract as a whole needs an **owning team and an escalation path** — a perfect contract on a dataset nobody owns turns every breach into an orphan ticket; if no owner exists, surfacing that is itself a primary finding.
6. **Implement in the repo's existing framework — and price each check.** dbt tests / Dataform assertions / Great Expectations / Airflow-run SQL checks — mirror what exists; don't introduce a new framework for the demo of one table. Estimate what each check costs to run (bytes scanned / runtime at its schedule): a daily full-table scan on a large table can out-cost the pipeline it guards — window, sample, or partition-scope expensive checks, or run them less often. Contract document lives next to the code (e.g. `contracts/<dataset>.md` or schema YAML), versioned in git.
7. **Backtest before enabling.** Run every check against the last 30–90 days of history: each firing is either a real past incident (good — the check works) or a false alarm (recalibrate). Report the backtest results; enable only after false alarms are resolved.
8. **Wire alerting and test the failure path.** Force one check to fail in a dev run and confirm the alert arrives where the on-call human actually looks, with the dataset, check, breach value, and runbook link.
9. **Hand off** per the output format: the contract, check implementation, backtest evidence, breach policies, and the downstream-impact map. Before handing off, load `references/checklist.md` and verify every item; consult `references/anti-patterns.md` when calibrating thresholds or choosing breach policies.

## Questions to ask (only when necessary)

- The freshness SLA consumers truly need, when usage patterns can't reveal it (a daily 9am exec dashboard implies one; an ML featurestore another).
- Business invariants only domain experts know ("can a refund exceed the original?").
- Who should be paged vs. Slack-notified per breach class, if no convention exists.

Current data behavior is never asked — it's profiled.

## Quality bar

- Every expectation traceable to a named consumer dependency or business rule — no checks-for-checks'-sake.
- Every threshold justified by profiled data, with the query/number recorded.
- Every check has an explicit breach policy and a notified owner; the contract has an owning team and escalation path.
- Check cost estimated; expensive checks windowed, sampled, or rescheduled deliberately.
- Backtest run over history; false-alarm rate addressed before enabling.
- The alert path was exercised once, deliberately.
- Contract is versioned in git, adjacent to the pipeline code, with a change policy for the schema.

## Expected output format

```markdown
## Data quality contract: <dataset>

**Consumers**: <who + which columns/properties they depend on (evidence, or "assumed — logs unavailable")>
**Owning team / escalation**: <team + path, or "no owner — flagged as finding">
**Grain**: one row per <X>

**Contract** (per check)
| dimension | expectation | threshold (calibration evidence) | breach policy | owner/alert |
|---|---|---|---|---|

**Implementation**: <framework + files>
**Backtest**: <period; firings: N real incidents caught, M false alarms → recalibrations>
**Alert path test**: <forced failure on <date> → arrived in <channel>: yes>

**Schema change policy**: <additive vs breaking process>
**Known gaps** (checked, deliberately excluded): <what and why>
```

## Failure modes to avoid

- **Imagination-calibrated thresholds**: "rows should never drop 10%" — they drop 40% every Sunday. Profile first.
- **Everything-is-an-alert**: no blocking checks, so grain violations flow downstream while a Slack message scrolls by; or everything blocks and the pipeline cries wolf.
- **Unowned alerts**: checks firing into a channel nobody watches — discovered during the next incident.

More in `references/anti-patterns.md`.
