# Backfill Planning & Verification — Checklist

## Scope
- [ ] Affected rows/partitions bounded by the change (date range, partitions, tenants/keys)
- [ ] Affected-row count proven with a query (not "reload everything")
- [ ] Out-of-scope data explicitly identified as must-not-change

## Validate the fix first
- [ ] Fixed logic recomputed on a sample into a scratch location
- [ ] Sample diffed against current (wrong) values AND an independent correct reference
- [ ] Confirmed the fix actually produces correct values before the full run

## Idempotent execution
- [ ] Write mechanism chosen: partition-replace / MERGE on key / delete+reinsert-by-range (not blind append)
- [ ] Each batch is safe to run twice; double-run safety stated (ideally shown)

## Batching
- [ ] Chunked by partition/date/key range, not one monster statement
- [ ] Batch size, ordering, and progress tracking/resume defined
- [ ] Per-batch failure domain is bounded

## Cost & load
- [ ] Bytes scanned/written per batch estimated (dry-run/EXPLAIN)
- [ ] Total spend and runtime estimated before running
- [ ] Contention with production jobs considered; low-traffic window chosen if material

## Isolation
- [ ] Exposure model decided (shadow table + swap vs in-place)
- [ ] Downstream consumers won't silently read partially-backfilled data
- [ ] Downstream owners notified/coordinated

## Validation (defined before running)
- [ ] Counts pre/post per batch
- [ ] Target metric now matches its reference (reconciliation)
- [ ] Unaffected partitions provably unchanged (checksum/count)
- [ ] Sampled rows spot-checked

## Rollback
- [ ] Pre-backfill state retained (snapshot/clone or reversible range)
- [ ] Exact undo steps stated
- [ ] Pending evidence listed where access was missing
