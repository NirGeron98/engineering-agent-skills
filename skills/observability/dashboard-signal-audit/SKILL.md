---
name: dashboard-signal-audit
description: Review an operational dashboard for actionable signal, missing panels, misleading charts, vanity metrics, alert alignment, or incident usefulness. Use when a dashboard signal audit, actionable-vs-vanity metric findings, missing panels, misleading visualizations, an incident-usefulness score, and recommended dashboard changes need to be produced from the actual dashboard.
---

# Dashboard Signal Audit

## Goal

Turn an actual operational dashboard into an evidence-based audit of what helps during an incident versus what is just wallpaper.

## When to use

- An operational dashboard needs review for actionable signal, missing panels, or misleading visualizations.
- On-call reports the dashboard isn't useful during incidents.
- The user needs an audit distinguishing vanity metrics from actionable ones.

## When not to use

- The task is business/data-analysis reporting dashboards, not operational/on-call use; defer to future data-analysis skills.
- The task is alert threshold design; use `skills/observability/metrics-and-alerts-review`.
- The task is deciding the source-of-truth SLOs the dashboard should reflect; use `skills/observability/slo-definition-review`.

## Composition and handoff rules

- Defer data analysis dashboards or business reporting to future data-analysis skills.
- Defer alert threshold design to `skills/observability/metrics-and-alerts-review`.
- Defer SLO source-of-truth decisions to `skills/observability/slo-definition-review`.

## Evidence access rule

Inspect the actual dashboard (panels, queries, chart types, time ranges) and, where possible, how it was used (or not) during a real recent incident. If the dashboard definition or incident usage evidence is unavailable, request exact exports/screenshots and mark usefulness-score conclusions pending.

## Workflow

1. Inventory the dashboard's actual panels: what each shows, its query/source, and chart type.
2. Classify each panel as actionable (drives a decision during an incident) or vanity (looks informative but doesn't change what on-call does).
3. Identify missing panels for known critical failure modes not currently visualized.
4. Identify misleading visualizations (misleading aggregation, wrong time range default, chart type that hides spikes).
5. Score incident usefulness based on evidence from a real recent incident if available, or mark it as an untested hypothesis if not.
6. Recommend concrete dashboard changes (add/remove/reorder panels, fix aggregation, align with current alerts/SLOs).
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to treat panel count as coverage or accept a dashboard as useful without incident evidence.

## Questions to ask (only when necessary)

- Was this dashboard actually opened during the most recent related incident, and did it help?

## Quality bar

- Actionable-vs-vanity classification is tied to actual on-call decision-making, not panel aesthetics.
- Incident-usefulness score is evidence-based or explicitly marked as untested.
- Recommendations are specific panel changes, not generic "add more monitoring."

## Expected output format

```markdown
## Dashboard signal audit: <dashboard>

**Dashboard signal audit**: <summary>
**Actionable vs vanity metric findings**: <panel - classification - why>
**Missing panels**: <failure modes not covered>
**Misleading visualizations**: <details>
**Incident-usefulness score**: <evidence-based or marked untested>
**Recommended dashboard changes**: <specific edits>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Dashboard as wallpaper**: not flagging panels that look informative but never inform a decision.
- **Panel-count-as-coverage**: assuming more panels means better incident coverage.
- **Untested usefulness claim**: scoring incident usefulness without any real incident evidence and presenting it as settled.

More in `references/anti-patterns.md`.
