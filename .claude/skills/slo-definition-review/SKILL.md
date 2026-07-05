---
name: slo-definition-review
description: Define or review service-level indicators, service-level objectives, error budgets, reliability targets, or alerting tied to user experience. Use when proposed or reviewed SLIs/SLOs, user journey mapping, measurement source, threshold rationale, alerting implications, and error-budget policy notes are needed from actual usage/traffic evidence.
---

# SLO Definition Review

## Goal

Turn actual user journey and traffic evidence into defensible SLIs/SLOs and error-budget policy, not copied targets.

## When to use

- Service-level indicators, objectives, error budgets, or reliability targets need to be defined or reviewed.
- The user needs SLOs tied to actual user experience and traffic patterns, not industry defaults.

## When not to use

- The task is alert configuration mechanics once SLOs exist; use `.claude/skills/metrics-and-alerts-review`.
- The task is dashboard panel/layout audit; use `.claude/skills/dashboard-signal-audit`.
- The task is incident process design; use `.claude/skills/incident-triage-runbook`.

## Composition and handoff rules

- Defer alert configuration mechanics to `.claude/skills/metrics-and-alerts-review`.
- Defer dashboard audit to `.claude/skills/dashboard-signal-audit`.
- Defer incident process to `.claude/skills/incident-triage-runbook`.

## Evidence access rule

Inspect actual traffic patterns, existing metrics, past incident/latency data, and user journeys before proposing thresholds. If traffic data, existing metrics, or user journey documentation is unavailable, request exact queries/exports and mark threshold rationale pending.

## Workflow

1. Identify the critical user journey(s) this service supports and what "good" looks like from the user's perspective.
2. Choose SLIs that measure that experience directly (latency, availability, correctness) using an actual available measurement source.
3. Propose or review SLO thresholds backed by historical traffic/latency/error data, not an industry-standard default copied without justification.
4. Define the error budget and its policy implications (what happens when it's spent).
5. Note alerting implications (burn-rate alerting, budget-based paging) at a policy level, deferring implementation specifics.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to copy a target from another service or pick a round-number threshold without data.

## Questions to ask (only when necessary)

- Who owns the error-budget policy decision (what action is taken when budget is exhausted)?

## Quality bar

- SLIs are traced to actual user journeys, not generic defaults.
- SLO thresholds are backed by historical data or explicitly marked as a starting hypothesis to validate.
- Error-budget policy names an owner and consequence.

## Expected output format

```markdown
## SLO definition review: <service/journey>

**Proposed or reviewed SLIs/SLOs**: <indicator - objective>
**User journey mapping**: <what "good" means for the user>
**Measurement source**: <where the SLI is measured from>
**Threshold rationale**: <historical data or explicit hypothesis>
**Alerting implications**: <burn-rate/policy level>
**Error-budget policy notes**: <owner/consequence>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **SLO copied from another service**: reusing "99.9%" or similar without checking this service's actual traffic/latency profile.
- **Vanity metrics as reliability**: choosing an SLI that looks good but doesn't reflect real user experience.
- **Unowned error budget**: defining a budget with no policy for what happens when it's exhausted.

More in `references/anti-patterns.md`.
