# Data Freshness Investigation — Checklist

## Quantify
- [ ] Newest record/partition in the stale output queried (max event_time, max _loaded_at, latest partition)
- [ ] Expected freshness/SLA stated
- [ ] Load-time vs event-time gap distinguished

## Topology
- [ ] Immediate upstream(s) of the stale output identified from lineage/DAG/ref(), not guessed
- [ ] Chain traced back to raw ingestion and the operational source

## Localize
- [ ] Each stage's max timestamp / last successful run checked from the top down
- [ ] Break localized between last-fresh and first-stale stage
- [ ] Only the broken stage read in depth (not the whole pipeline)

## Classify the break
- [ ] Determined: didn't run / ran-loaded-nothing / source stopped producing / ran but not visible
- [ ] Scheduler run history + last exit status checked (for didn't-run)
- [ ] Watermark / filter / timezone boundary checked (for loaded-nothing)
- [ ] True source verified directly (for source-stopped) — not inferred from empty table
- [ ] Materialization/view/cache/partition refresh checked (for not-visible)

## Prove
- [ ] One direct query establishes the classification (watermark vs source max, run row, hourly source counts)
- [ ] Result recorded

## Impact & remediation
- [ ] Downstream stale models/dashboards enumerated with since-when
- [ ] Whether stale data was silently served noted
- [ ] Stage-specific fix stated
- [ ] Backfill need + scope stated; handed off to backfill-planning-and-verification if nontrivial
- [ ] Missing freshness guardrail named (→ data-quality-contracts)
- [ ] Pending evidence listed where access was missing
