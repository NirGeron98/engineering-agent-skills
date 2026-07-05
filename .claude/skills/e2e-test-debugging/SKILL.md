---
name: e2e-test-debugging
description: Diagnose a failing, flaky, or unclear end-to-end test using browser, screenshot, video, trace, console, network, selector, timing, auth/session, API, test-data, and environment evidence. Use when the output should be an E2E failure diagnosis, evidence table, root-cause classification, and stabilization or minimal-fix plan.
---

# E2E Test Debugging

## Goal

Diagnose an E2E test failure from runtime evidence, classify the root cause, and produce the smallest fix or stabilization plan without guessing visual, browser, network, or timing behavior.

## When to use

- A Playwright, Cypress, Selenium, Webdriver, or similar E2E test fails or flakes.
- Failure output is unclear and needs screenshots, video, trace, console, or network evidence.
- The suspected issue may be selector, timing, auth/session, API response, test data, environment drift, or app behavior.

## When not to use

- The boundary is specifically frontend/API product behavior; use `.claude/skills/api-integration-debugging`.
- The proven issue is a deployed service failure; use `.claude/skills/service-debugging`.
- The flake is not E2E-specific; use `.claude/skills/flaky-test-debugging`.

## Composition and handoff rules

- Hand frontend/API boundary issues to `.claude/skills/api-integration-debugging`.
- Hand service runtime issues to `.claude/skills/service-debugging`.
- Hand general flaky behavior to `.claude/skills/flaky-test-debugging`.

## Evidence access rule

Do not infer browser state, layout, network, console errors, auth/session, or timing from test code alone. Inspect or request exact test output, trace/video/screenshot, console logs, network/HAR, app logs, commit/CI info, and reproduction commands; mark dependent conclusions pending.

## Workflow

1. Record test name, environment, command, browser, retry count, recent changes, and whether the failure is deterministic.
2. Inspect failure artifacts: screenshot, video, trace, console, network/HAR, test logs, app logs, and test data setup.
3. Compare expected vs actual user flow, selectors, waits, auth/session state, API responses, environment config, and data setup/cleanup.
4. Classify root cause: app defect, selector brittleness, timing/wait issue, test data, auth/session, API/backend, environment drift, or true flake.
5. Propose the minimal fix or stabilization plan and verification command, including retry policy only as containment.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to add sleeps, trust screenshots without traces, or treat retries as a fix.

## Questions to ask (only when necessary)

- Which CI run or artifact URL should be treated as the canonical failure?

## Quality bar

- Runtime claims cite trace, screenshot, video, network, console, logs, or requested evidence.
- Root cause classification separates app defect from test instability.
- The stabilization plan includes a deterministic verification loop.

## Expected output format

```markdown
## E2E failure diagnosis: <test>

**Evidence reviewed**: <logs/traces/screenshots/network/commands>
**Evidence table**: <artifact - observation - implication>
**Root cause classification**: <category and confidence>
**Ruled-out causes**: <cause - evidence>
**Fix or stabilization plan**: <minimal steps>
**Verification**: <command/results or pending requests>
```

## Failure modes to avoid

- **Sleep as diagnosis**: adding arbitrary waits before proving the race.
- **Screenshot-only certainty**: declaring cause from one visual artifact without DOM/network/timing evidence.
- **Retry hides failure**: treating retry success as repair.

More in `references/anti-patterns.md`.
