---
name: load-test-result-review
description: Review load test results before a launch, scaling decision, performance fix, or reliability decision, covering test scenario realism, environment parity, error rates, latency percentiles, and saturation point. Use when a go/no-go recommendation is needed from load test evidence. Produces a load test result review with validity concerns, bottleneck findings, and a go/no-go recommendation.
---

# Load Test Result Review

## Goal

Turn a load test report into an evidence-backed judgment of whether the test is valid and what it actually proves about readiness.

## When to use

- Load test results need review before a launch, scaling decision, or reliability sign-off.
- The realism of the test scenario (workload shape, ramp pattern, environment parity) is in question.
- A go/no-go recommendation is needed from the test evidence, not from the tuning work itself.

## When not to use

- The task is release/rollout process readiness broadly, not the load test evidence itself; use `.claude/skills/release-and-rollback-readiness`.
- The question is diagnosing why throughput is capped, not reviewing a completed test's validity; use `.claude/skills/throughput-bottleneck-triage`.
- The question is whether SLOs/alerts are well-designed, not the load test itself; use `.claude/skills/slo-definition-review`.

## Composition and handoff rules

- Defer release readiness to `.claude/skills/release-and-rollback-readiness`.
- Defer throughput diagnosis to `.claude/skills/throughput-bottleneck-triage`.
- Defer SLO design to `.claude/skills/slo-definition-review`.

## Evidence access rule

Inspect the actual load test configuration (scenario, ramp pattern, duration), the target environment's parity with production, and the raw results (error rates, latency percentiles, resource utilization during the test). If the test configuration or raw results are unavailable, request exact exports/logs and mark validity and bottleneck conclusions pending.

## Workflow

1. Confirm what the test was meant to prove (target QPS/latency/reliability) and the actual scenario used.
2. Assess workload realism: request mix, ramp pattern, and think time versus real production traffic shape.
3. Assess environment parity: is the test environment representative of production scale/config, or smaller/different.
4. Review error rates and latency percentiles (not just averages) against the target, and identify the saturation point.
5. State the test's limits explicitly (what it does and doesn't prove) alongside bottleneck findings.
6. Produce a go/no-go recommendation with named follow-up tests if gaps remain.
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to treat a passing synthetic test as launch proof.

## Questions to ask (only when necessary)

- What target QPS/latency/error-rate was this test meant to validate?
- How does the test environment compare to production (scale, config, data volume)?

## Quality bar

- Workload realism and environment parity are assessed explicitly, not assumed adequate.
- Latency percentiles (not just averages) and error rates are reviewed.
- The go/no-go recommendation states the test's limits, not just its headline pass/fail.

## Expected output format

```markdown
## Load test result review: <scope>

**Evidence summary**: <scenario, ramp, environment parity, error rates, latency percentiles>
**Validity concerns**: <realism/parity gaps>
**Bottleneck findings**: <saturation point, evidence>
**Go/no-go recommendation**: <verdict and reasoning>
**Follow-up tests**: <if gaps remain>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Synthetic load as launch proof**: treating a passing test under an unrealistic workload/environment as sufficient launch evidence.
- **Average latency hides tail pain**: judging the test a pass based on average latency while p95/p99 or error rates were bad.
- **Environment parity gap ignored**: extrapolating small-scale test results directly to production scale without caveat.

More in `references/anti-patterns.md`.
