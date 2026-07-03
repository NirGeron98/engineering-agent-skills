# Dashboard Signal Audit Checklist

- [ ] Actual dashboard panels, queries, and chart types were inspected, not assumed.
- [ ] Each panel is classified actionable vs vanity based on real on-call decision-making.
- [ ] Missing panels are checked against known critical failure modes.
- [ ] Misleading visualizations (aggregation, time range, chart type) are identified from the actual config.
- [ ] Incident-usefulness score is backed by real incident evidence or explicitly marked untested.
- [ ] Recommended changes are specific panel-level edits.
- [ ] Missing dashboard definition or incident usage evidence is marked pending.
- [ ] Panel count is not used as a proxy for coverage.
