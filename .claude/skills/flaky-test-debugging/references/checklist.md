# Flaky Test Debugging Checklist

- [ ] Failing test, command, environment, failure message, first-seen time, retry behavior, and frequency are recorded.
- [ ] Multiple passing/failing runs or exact repeated-run requests are included.
- [ ] Seed, time, order, parallelism, worker count, shared state, external services, and environment differences are checked.
- [ ] Hypotheses are falsifiable and rejected hypotheses are recorded.
- [ ] Containment is separated from repair.
- [ ] Quarantine or retry policy has owner, expiry, and evidence plan if used.
- [ ] Verification loop proves repeated pass under formerly failing conditions.
- [ ] Missing CI logs, seeds, run history, artifacts, or environment metadata are marked pending.

