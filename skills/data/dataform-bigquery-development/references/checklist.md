# Dataform BigQuery Development — Checklist

## Orientation
- [ ] workflow_settings.yaml / dataform.json read (datasets, vars)
- [ ] Layer + naming convention mapped from definitions/ and followed
- [ ] All upstreams via ref(); zero hardcoded table paths
- [ ] Dependents of touched models enumerated (compile graph)

## Source understanding
- [ ] Upstream grain verified by query (GROUP BY key HAVING COUNT(*)>1)
- [ ] Null rates on join/filter keys checked
- [ ] Time column semantics confirmed (event vs load time, timezone)

## Model config
- [ ] type appropriate (view / table / incremental justified by volume)
- [ ] Grain documented in the model and enforced by uniqueness assertion
- [ ] Partitioning on the dominant time filter; clustering on frequent keys
- [ ] Consumers can prune (they filter the partition column directly)

## Incremental contract (if applicable)
- [ ] uniqueKey set (or append-only explicitly justified)
- [ ] Lookback window in the incremental filter, sized and justified
- [ ] Incremental filter prunes partitions on source and target
- [ ] Re-run same window: no duplicates (tested)
- [ ] Full-refresh behavior and cost stated

## Assertions
- [ ] uniqueKey + nonNull on grain columns
- [ ] Business-rule/custom assertions where contracts exist (freshness, ranges, ref integrity)
- [ ] All assertions green in dev workspace

## Verification & impact
- [ ] Commands/queries you couldn't run yourself handed to the user; results pasted back, not assumed
- [ ] dataform compile clean
- [ ] Row counts plausible vs upstream; 5-row spot-check vs source
- [ ] Modified models: dev output diffed against production output on a bounded window; differences explained by the intended change
- [ ] bq dry-run: bytes scanned acceptable; regressions explained
- [ ] Dependents grepped for renamed/retyped/dropped columns
- [ ] Coordination or deprecation plan for breaking column changes
