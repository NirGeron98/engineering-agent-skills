# Diagnose Bug — Checklist

## Symptom & reproduction
- [ ] Exact error text / expected-vs-actual captured verbatim
- [ ] Environment, frequency, and "last known good" recorded
- [ ] Reliable reproduction built (failing test, script, curl) — or its absence flagged
- [ ] If unreproducible: targeted logging/assertions added so the next occurrence is diagnosable; any patch labeled speculative

## Diagnosis
- [ ] Failure boundary located: last-correct → first-wrong point in the data path
- [ ] Full relevant code read at the boundary, not just the crashing line
- [ ] ≤3 falsifiable hypotheses, ranked
- [ ] Each hypothesis tested with evidence; rejections recorded in one line
- [ ] Root cause explains the full causal chain and predicts reproduction behavior
- [ ] If regression: `git log` / `git bisect` consulted

## Fix
- [ ] Regression test written first and observed failing for the right reason
- [ ] Fix targets the cause, not the symptom
- [ ] Diff minimal — no refactors, formatting, or drive-bys
- [ ] Same defect pattern searched for across the codebase

## Verification & handoff
- [ ] Regression test + relevant suite pass
- [ ] Original reproduction re-run and correct
- [ ] Handoff summary written with evidence, rejected hypotheses, and sibling sites
