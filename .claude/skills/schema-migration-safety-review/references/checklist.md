# Schema Migration Safety Review Checklist

- [ ] The actual migration diff/script was inspected, not just its description.
- [ ] Target table size was obtained or requested before assessing lock/downtime risk.
- [ ] Lock behavior for this specific operation type on this engine is stated, not assumed.
- [ ] Compatibility between old and new app versions during rollout is explicitly addressed.
- [ ] Backfill strategy (batching, throttling, monitoring) is included if a backfill is present.
- [ ] Rollback/roll-forward plan is verified against the actual migration, not asserted as "reversible."
- [ ] A data validation checklist exists to confirm correctness after migration/backfill.
- [ ] Deployment order (schema-first vs app-first) is explicitly stated.
- [ ] Missing table size, migration script, or app-compatibility info is marked pending.
- [ ] Ownership for monitoring the rollout is named.
