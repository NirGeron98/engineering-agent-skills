# Data Quality Contracts — Checklist

## Discovery
- [ ] Consumers identified with evidence (query logs, BI usage, cross-repo grep)
- [ ] Where query logs / BI usage were unreachable: fallback used (grep BI repos, downstream refs) and unverified consumer claims labeled as assumptions
- [ ] Queries you couldn't run yourself handed to the user; results pasted back, not assumed
- [ ] Column/property dependencies per consumer recorded
- [ ] Data profiled: counts + weekly pattern, null rates, categorical domains, ranges, arrival pattern, duplicate rate on assumed key

## Contract content
- [ ] Grain stated and checked (uniqueness on key)
- [ ] Schema expectations + change policy (additive vs breaking)
- [ ] Nullability limited to consumer-critical columns
- [ ] Freshness SLA confirmed with consumers and achievable by the pipeline schedule
- [ ] Volume band seasonally aware (day-of-week, month-end)
- [ ] Business invariants captured from domain knowledge
- [ ] Every expectation traceable to a consumer need — no orphan checks

## Calibration & policy
- [ ] Every threshold derived from profiled data, evidence recorded
- [ ] Breach policy per check: block / quarantine / alert-only — deliberate
- [ ] Owner + notification target named per check
- [ ] Contract as a whole has an owning team and escalation path (or its absence flagged as a finding)
- [ ] Cost per check estimated (bytes scanned / runtime at schedule); expensive checks windowed, sampled, or rescheduled

## Implementation & verification
- [ ] Implemented in the repo's existing framework (no new tooling by default)
- [ ] Contract document versioned in git next to the pipeline
- [ ] Backtested over 30–90 days; false alarms recalibrated; results recorded
- [ ] Alert path exercised with a forced failure
- [ ] Runbook/next-step linked from the alert
