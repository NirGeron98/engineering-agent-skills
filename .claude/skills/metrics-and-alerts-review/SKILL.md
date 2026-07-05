---
name: metrics-and-alerts-review
description: Review alert coverage, alert noise, missing alerts, poor thresholds, paging quality, or metrics used for operational decisions. Use when an alert inventory, signal/noise findings, missing coverage, threshold recommendations, ownership/escalation gaps, and a verification plan need to be produced from actual alert definitions and paging history.
---

# Metrics and Alerts Review

## Goal

Turn actual alert definitions and paging history into an evidence-based review of alert coverage, noise, and threshold quality.

## When to use

- Alert coverage, noise, missing alerts, or paging quality need review.
- Metrics used for operational decisions need a signal-quality assessment.
- The user needs threshold recommendations or ownership/escalation gap findings.

## When not to use

- An incident is actively in progress; use `.claude/skills/incident-triage-runbook`.
- SLI/SLO design is the primary task; use `.claude/skills/slo-definition-review`.
- Dashboard layout/panel review is the primary task; use `.claude/skills/dashboard-signal-audit`.

## Composition and handoff rules

- Defer active incident handling to `.claude/skills/incident-triage-runbook`.
- Defer SLO/SLI design to `.claude/skills/slo-definition-review`.
- Defer dashboard layout/signal review to `.claude/skills/dashboard-signal-audit`.

## Evidence access rule

Inspect actual alert definitions, thresholds, paging/on-call history, and metrics sources. If alert configs, paging history, or metric definitions are unavailable, request exact exports/queries and mark coverage and noise conclusions pending.

## Workflow

1. Inventory existing alerts: what they monitor, threshold, severity, and where they page.
2. Assess signal/noise using paging history: which alerts fire often without action, and which real incidents had no alert.
3. Identify missing coverage for known critical failure modes not currently alerted on.
4. Recommend threshold changes backed by historical data, not guesses.
5. Identify ownership/escalation gaps (alerts with no clear owner or unclear escalation path).
6. Propose a verification plan (test alert firing, review after a set period).
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to accept noisy alerts as normal or recommend thresholds without historical evidence.

## Questions to ask (only when necessary)

- What is the acceptable page-per-week budget for this on-call rotation?

## Quality bar

- Noise and coverage findings are based on actual paging/alert history, not intuition.
- Threshold recommendations cite historical data ranges.
- Ownership gaps are explicit, not assumed resolved.

## Expected output format

```markdown
## Metrics and alerts review: <scope>

**Alert inventory**: <alert - threshold - severity - destination>
**Signal/noise findings**: <noisy alerts, silent gaps>
**Missing coverage**: <failure modes without alerts>
**Threshold recommendations**: <evidence-backed>
**Ownership/escalation gaps**: <details>
**Verification plan**: <steps>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Alert noise accepted as normal**: not flagging alerts that page frequently without action.
- **Threshold guessing**: recommending a new threshold without historical data.
- **Coverage assumed**: declaring a failure mode "covered" without checking an alert actually exists for it.

More in `references/anti-patterns.md`.
