# DB-to-DWH Pipeline — Checklist

## Source profiling (queried, not assumed)
- [ ] Profiling run against a replica (or off-peak with bounded queries); source load considered
- [ ] Queries you couldn't run yourself handed to the user verbatim; results pasted back, not assumed
- [ ] Row counts, daily churn measured
- [ ] Primary key uniqueness verified by query
- [ ] updated_at reliability checked against write paths (ORM config, triggers, bulk jobs)
- [ ] Delete behavior determined: hard / soft / never
- [ ] Column types + real nullability sampled

## Design
- [ ] Existing ingestion framework in repo checked and mirrored (or deviation justified)
- [ ] Full source-to-target mapping: every column carried/renamed/converted/excluded
- [ ] Lossy type conversions flagged; timezone handling explicit
- [ ] PII columns identified with explicit drop/hash/carry decision
- [ ] Metadata columns added (_loaded_at, _extracted_at, source)

## Incremental correctness
- [ ] Strategy chosen from the profile (watermark / CDC / snapshot / full reload)
- [ ] Lookback window sized and justified (late-committing transactions)
- [ ] "What row change could this miss" analysis written
- [ ] Delete propagation handled or explicitly declined with reason
- [ ] Schema drift policy stated: new col / type change / dropped col

## Reliability
- [ ] Load idempotent (MERGE / partition replace) — proven by double-run
- [ ] Backfill path separate from incremental; verified before cutover
- [ ] Backfill cost/load estimated before running (bytes, source read pressure, runtime)
- [ ] Reconciliation in pipeline: row counts, freshness lag vs SLA, checksum/sample on one key table
- [ ] Failures alert somewhere a human looks

## Handoff
- [ ] Lineage documented: source → landing → staging
- [ ] Downstream consumers identified
- [ ] Verified-slice results included (counts, double-run, delete test)
