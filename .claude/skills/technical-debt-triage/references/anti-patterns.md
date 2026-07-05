# Technical Debt Triage Anti-patterns

## Debt list without prioritization

- **Symptom**: The output restates complaints as a flat list.
- **Why it matters**: The team still cannot decide what to do next.
- **Correction**: Rank by evidence of impact, risk, cost of delay, and dependencies.

## Fixing everything at once

- **Symptom**: Every debt item becomes urgent.
- **Why it matters**: It creates a hidden rewrite plan and competes with product work.
- **Correction**: Choose a small next set and explicitly defer the rest.

## Loudest pain wins

- **Symptom**: The most complained-about area gets top priority without evidence.
- **Why it matters**: Irritation is not the same as impact.
- **Correction**: Ask for or inspect churn, incidents, flaky tests, support cost, and delivery delays.

## Debt as moral judgment

- **Symptom**: The review frames past decisions as bad engineering.
- **Why it matters**: It reduces trust and hides original constraints.
- **Correction**: Describe current impact and tradeoffs neutrally.

## Refactor plan in disguise

- **Symptom**: The triage jumps into migration steps for one item.
- **Why it matters**: Prioritization gets skipped.
- **Correction**: Rank first, then hand the selected item to `refactor-strategy`.
