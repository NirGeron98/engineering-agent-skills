# Diagnose Bug — Anti-patterns

## Shotgun patching
**Symptom**: change a line, rerun, change another, rerun — until the error stops appearing.
**Why it fails**: the symptom may vanish while the defect remains; you can no longer tell which change mattered; evidence is destroyed.
**Instead**: one hypothesis, one probe, one recorded result.

## Null-check whack-a-mole
**Symptom**: `if (x != null)` added at the crash site of a `NullPointerException`-class failure.
**Why it fails**: the question is why `x` was null; the guard converts a loud failure into silent wrong behavior.
**Instead**: trace where the null was introduced; fix or reject it there.

## Crash site = cause site
**Symptom**: diagnosing only the function in the last stack frame.
**Instead**: walk backwards to where the bad state was created — often several calls (or one queue hop) earlier.

## "Cannot reproduce" → "probably fixed"
**Symptom**: unable to reproduce, so a plausible patch is shipped and the ticket closed.
**Instead**: say clearly it's unreproduced; add targeted logging/assertions so the next occurrence is diagnosable; label the patch as speculative.

## Retry-and-pray for flaky failures
**Symptom**: intermittent failure "fixed" by adding a retry or sleep.
**Instead**: intermittency is a clue (race, ordering, shared state, time dependence) — instrument to catch it; a retry may be a legitimate mitigation but never the diagnosis.

## Fixing the test to match the bug
**Symptom**: the failing test's expectation is edited so the suite goes green.
**Instead**: first determine which is wrong — code or test — from the spec/intended behavior; state the determination explicitly.

## Environment-blind reproduction
**Symptom**: the bug bites in production under real data volume and concurrency; the "reproduction" runs one record locally, the fix passes, victory declared.
**Why it fails**: the reproduction never exercised the condition that actually triggers the defect — the fix may be irrelevant or partial.
**Instead**: match the reproduction to the failing environment's relevant properties (versions, data shape, concurrency, timing) or state explicitly which properties it does not reproduce.

## Kitchen-sink bugfix PR
**Symptom**: the fix commit also renames variables, reorders imports, refactors a helper.
**Instead**: minimal diff; list wanted cleanups as follow-ups.
