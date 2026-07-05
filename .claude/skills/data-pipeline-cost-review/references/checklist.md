# Data Pipeline Cost Review — Checklist

## Cost distribution (measure first)
- [ ] Job/query history queried for the period (bytes scanned / slot-seconds / credits)
- [ ] Cost ranked by total = frequency × per-run, not per-run alone
- [ ] Billing model identified (on-demand bytes / flat-rate slots / credits)
- [ ] Top ~10 drivers identified and their share of total computed

## Attribution
- [ ] Each top driver's SQL/config read
- [ ] Mechanism identified per driver (full scan / non-incremental rebuild / over-frequent / wide read / redundant materialization / skew-join)
- [ ] Partition-pruning actually verified (filter prunes; no function-on-partition-column)

## Savings estimate
- [ ] Projected cost estimated from evidence (dry-run / bytes-after-pruning), not guessed
- [ ] Saving = current − projected, multiplied by run frequency

## Risk of each fix
- [ ] Correctness risk noted (incremental late-data/idempotency handling)
- [ ] Freshness risk noted (cadence vs SLA; cross-checked with contracts if any)
- [ ] Consumer impact noted (dropped columns / changed materialization)
- [ ] "Unused" models verified via lineage/query history before proposing deletion

## Structural wins
- [ ] Partitioning/clustering of largest scanned tables considered
- [ ] Required partition filters considered
- [ ] Result caching not needlessly disabled
- [ ] Hot repeated CTE / redundant intermediate identified
- [ ] Zombie jobs / dead models flagged (with usage evidence)

## Prioritize & hand off
- [ ] Fixes ranked by saving vs risk/effort
- [ ] Total addressable saving stated with confidence
- [ ] In-place rewrites routed to dataform-bigquery-development; recomputes to backfill-planning-and-verification
- [ ] Pending evidence listed where access was missing
