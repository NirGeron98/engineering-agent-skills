# Data Quality Contracts — Anti-patterns

## Thresholds from imagination
**Symptom**: "alert if volume drops >10%" — volume drops 40% every weekend; the alert is muted by week two.
**Instead**: profile the seasonal pattern first; band per day-of-week or use a week-over-week comparison.

## The muted-alert graveyard
**Symptom**: a channel of daily red checks everyone has learned to ignore; the real incident scrolls past unread.
**Why it fails**: a noisy check is worse than none — it consumes the team's alarm response and then discredits the whole system.
**Instead**: backtest before enabling; every false alarm is a calibration bug to fix, not a notification to tolerate.

## Null-check theater
**Symptom**: dozens of `not_null` tests on incidental columns; grain, freshness, and the invariant behind the last incident: unchecked.
**Instead**: derive checks from consumer dependencies and incident history; delete checks that protect nobody.

## All-alert, no-teeth
**Symptom**: a grain violation "alerts" while duplicate rows flow into every downstream join overnight.
**Instead**: corruption-class breaches (grain, schema break) block the pipeline; only soft degradations are alert-only.

## All-teeth, no-judgment
**Symptom**: pipeline blocks at 2am because one row of 40M has a null comment field.
**Instead**: match severity to blast radius; row-level nits quarantine or alert, they don't stop the freight train.

## Zero-tolerance on dirty reality
**Symptom**: hard-failing on a 0.3% null rate that has been stable for two years and affects no consumer.
**Instead**: encode reality with a tolerance band; drive the rate down as a separate improvement task if it matters.

## The million-dollar checksum
**Symptom**: a daily full-table checksum and six unpartitioned `not_null` scans on a 190M-row table — the quality suite quietly scans more bytes per month than the pipeline it guards.
**Why it fails**: nobody prices the checks; the bill surfaces in a cost review and the response is to delete the whole suite, protection included.
**Instead**: estimate bytes/runtime per check at its schedule; scope checks to the fresh partition or a sample where that preserves the guarantee; reserve full-table scans for weekly/monthly cadence with the cost stated in the contract.

## Contract for a table nobody owns
**Symptom**: a beautifully calibrated contract on a dataset with no owning team; every breach becomes a ticket that bounces between producers and consumers until it's closed as stale.
**Why it fails**: checks detect; only owners act. Detection without an escalation path is a notification, not a contract.
**Instead**: name the owning team and escalation path in the contract itself; if no owner exists, escalate that gap as the first finding — it blocks the contract's value, not just its paperwork.

## Contract in nobody's language
**Symptom**: expectations exist only as scattered test configs; consumers can't read what's guaranteed, producers change schemas unaware.
**Instead**: one human-readable contract document in git, adjacent to the pipeline, change-reviewed like code.

## Fire-and-forget quality project
**Symptom**: a heroic month of writing checks; a year later half are stale, thresholds outdated, new columns uncovered.
**Instead**: contracts change with the pipeline — schema PRs that touch the dataset must touch the contract, enforced by review convention.

## Unowned alerts
**Symptom**: check fires correctly into #data-quality; nobody's on rotation; the breach is found by the CFO.
**Instead**: every check names an owner and a destination someone actually watches; test the path with a forced failure.
