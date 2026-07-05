# Sample Task

## User prompt

"We have a long list of tech debt complaints: slow tests, duplicated validation, old auth middleware, huge shared utilities, and messy invoice code. Prioritize what to fix first and what to ignore for now."

## Abbreviated run

1. Reads the complaint list, issues, recent incidents, flaky test reports, and PR history if available.
2. Maps each item to impact, frequency, risk, blast radius, and cost of delay.
3. Finds old auth middleware has moderate pain but high security/change risk, while duplicated validation has frequent delivery cost.
4. Separates quick wins from strategic work.

## Obstacle moment

The agent cannot inspect incident history or PR churn.

Exact evidence requests:

```text
Please provide the last 3-6 months of incidents or support issues related to these debt areas.
Please paste PR/review examples where these debt items delayed work, or confirm that impact scoring should remain pending.
```

## Expected output

A prioritized debt list, impact/risk scoring, recommended next actions, defer/ignore list with reasons, and suggested tracking issues.

## Why this passes the quality bar

The triage ranks debt by evidence, explicitly says what not to fix, and hands chosen refactors to `refactor-strategy` rather than planning everything.
