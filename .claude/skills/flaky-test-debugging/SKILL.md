---
name: flaky-test-debugging
description: Diagnose a test that passes sometimes and fails sometimes in local or CI runs. Use when failure frequency, order/seed/time dependency, shared state, isolation, concurrency, external services, timeouts, retries, and environment differences need an evidence table, suspected causes, stabilization plan, and verification loop.
---

# Flaky Test Debugging

## Goal

Turn intermittent test failure into an evidence-backed diagnosis and stabilization plan by reproducing patterns, ruling out hypotheses, and avoiding retries as the primary fix.

## When to use

- A test passes and fails inconsistently.
- CI and local behavior differ without a clear deterministic failure.
- The user needs to identify order, time, seed, state, concurrency, environment, or external-service causes.

## When not to use

- The issue is primarily CI pipeline configuration; use `.claude/skills/ci-cd-debugging`.
- The failure is now reproducible as a code defect; use `.claude/skills/diagnose-bug`.
- The failure is E2E-specific and needs browser artifacts; use `.claude/skills/e2e-test-debugging`.

## Composition and handoff rules

- Hand CI pipeline environment issues to `.claude/skills/ci-cd-debugging`.
- Hand reproducible code defects to `.claude/skills/diagnose-bug`.
- Hand E2E-specific evidence handling to `.claude/skills/e2e-test-debugging`.

## Evidence access rule

Do not classify a flake without repeated evidence. Inspect or request exact commands, seeds, timestamps, retry logs, failing/passing runs, CI worker details, test order, artifacts, external-service calls, and recent changes; mark frequency and cause conclusions pending when evidence is missing.

## Workflow

1. Record the failing test, command, environment, failure message, first-seen time, retry behavior, and pass/fail frequency.
2. Gather repeated local/CI runs with seed, order, time, worker count, parallelism, and resource/environment metadata.
3. Test hypotheses for time, random seed, test order, shared state, isolation leak, concurrency, timeout, network/external service, and environment drift.
4. Separate containment from repair: quarantine or retry policy only with owner, expiry, and evidence plan.
5. Propose a stabilization fix and a verification loop that proves repeated pass under the formerly failing conditions.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to increase timeouts, add retries, or ignore failure frequency.

## Questions to ask (only when necessary)

- What quarantine policy or CI reliability threshold does the team require?

## Quality bar

- Frequency and suspected cause are backed by repeated runs or exact pending requests.
- Rejected hypotheses are recorded.
- The fix is verified under stress or repeated runs, not a single green rerun.

## Expected output format

```markdown
## Flaky test diagnosis: <test>

**Evidence reviewed**: <runs/logs/seeds/CI metadata>
**Failure pattern**: <frequency/order/time/env>
**Hypotheses tested**: <cause - evidence - status>
**Stabilization plan**: <fix/containment/owner/expiry>
**Verification loop**: <commands and pass criteria>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Retry as fix**: masking the flake without root-cause work.
- **Single-green proof**: declaring success after one passing run.
- **Timeout inflation**: increasing timeouts without identifying why timing varies.

More in `references/anti-patterns.md`.
