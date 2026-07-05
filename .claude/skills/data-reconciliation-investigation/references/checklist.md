# Data Reconciliation Investigation — Checklist

## Pin the discrepancy
- [ ] Both figures obtained with precise definitions (metric, filters, time range, grain, units, as-of)
- [ ] Confirmed the two are *supposed* to be equal (definitions actually match)
- [ ] Exact delta computed (absolute and %)

## Reproduce
- [ ] Each figure reproduced from its own source of truth with a query
- [ ] Any report-vs-its-own-query mismatch flagged as a finding

## Decompose
- [ ] Delta broken along a shared dimension (day/category/tenant), not compared as bare totals
- [ ] Delta shape characterized (constant / proportional / concentrated in a slice)

## Localize
- [ ] Reconciliation run at each transformation stage (source → staging → intermediate → mart)
- [ ] Both count and sum reconciled at each stage
- [ ] First stage of divergence identified

## Classify & prove
- [ ] Mechanism classified (double-count / dropped rows / grain / semantic / timing)
- [ ] Mechanism proven with the specific offending rows (duplicated keys / anti-join / mismatched values)

## Resolve
- [ ] Verdict on which figure is correct (or both wrong), backed by evidence
- [ ] Pipeline correction stated
- [ ] Restatement/backfill scope stated (→ backfill-planning-and-verification if nontrivial)
- [ ] Standing reconciliation check recommended (→ data-quality-contracts)
- [ ] Pending evidence listed where access was missing
