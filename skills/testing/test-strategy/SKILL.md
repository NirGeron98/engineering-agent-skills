---
name: test-strategy
description: Plan risk-based testing coverage for a feature, service, migration, repo, or subsystem. Use when unit, integration, e2e, contract, regression, manual, CI cost, critical-path, edge-case, and ownership tradeoffs need a concrete test strategy, risk matrix, and verification plan; defer test-first implementation, flaky failures, and E2E failure diagnosis to their specialist skills.
---

# Test Strategy

## Goal

Create an evidence-backed testing strategy that identifies the highest-risk behavior, chooses the right test layers, avoids wasteful coverage, and gives the team a concrete verification plan.

## When to use

- A feature, subsystem, migration, or service needs a testing plan before or during implementation.
- Existing coverage is unclear and the user needs to know which tests matter most.
- The team needs to balance unit, integration, e2e, contract, regression, and manual checks.

## When not to use

- The user wants to implement the change test-first; use `skills/testing/tdd-workflow`.
- A test is flaky or failing unpredictably; use `skills/testing/flaky-test-debugging`.
- An E2E/browser test failed and needs diagnosis; use `skills/testing/e2e-test-debugging`.
- The feature itself is still vague; use `skills/planning/task-breakdown`.

## Composition and handoff rules

- Hand test-first implementation slices to `skills/testing/tdd-workflow`.
- Hand flaky failure diagnosis to `skills/testing/flaky-test-debugging`.
- Hand E2E failure investigation to `skills/testing/e2e-test-debugging`.
- Hand feature planning to `skills/planning/task-breakdown`.

## Evidence access rule

Inspect the actual code paths, existing tests, CI commands, contracts, data flows, and known incidents before recommending coverage. If evidence is unavailable, request exact files, commands, test reports, specs, or risk context and mark dependent recommendations pending.

## Workflow

1. Define the behavior under test, users/consumers, critical paths, failure impact, and release constraints.
2. Inspect existing tests, CI/runtime limits, closest feature precedents, contracts, dependencies, and production or incident evidence if available.
3. Build a risk matrix covering likelihood, impact, observability, ownership, and regression history.
4. Choose test layers: unit, integration, contract, e2e, data/fixture checks, manual checks, and tests intentionally not worth adding yet.
5. Specify cases, fixtures, environments, commands, owners, and pass/fail evidence for the recommended checks.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to chase coverage numbers, overuse E2E tests, or skip CI cost.

## Questions to ask (only when necessary)

- Which user journeys or customers are most critical if the code evidence does not reveal that?
- What CI runtime or release constraints must the strategy respect?

## Quality bar

- Recommendations cite actual code, tests, contracts, CI, or supplied risk evidence.
- The strategy explains what not to test and why.
- Each recommended test has an owner, layer, evidence source, and verification command or request.

## Expected output format

```markdown
## Test strategy: <scope>

**Evidence reviewed**: <files/tests/CI/specs/incidents>
**Risk matrix**: <behavior - risk - impact - current coverage - recommended layer>
**Recommended tests**: <test cases by layer with commands/owners>
**Tests not worth adding yet**: <items and rationale>
**Manual or release checks**: <checks>
**Pending evidence**: <missing inputs and exact requests>
```

## Failure modes to avoid

- **Coverage theater**: optimizing for percentages instead of risk.
- **E2E everything**: pushing boundary or unit behavior into slow brittle browser flows.
- **Unowned verification**: listing tests without commands, owner, or pass/fail evidence.

More in `references/anti-patterns.md`.
