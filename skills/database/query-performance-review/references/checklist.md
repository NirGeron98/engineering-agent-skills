# Query Performance Review Checklist

- [ ] The actual query text and its caller/frequency context were inspected.
- [ ] An execution plan (not just query text) was obtained and read.
- [ ] Estimated vs actual row counts were compared at relevant plan nodes.
- [ ] Index usage (used, missing, or ignored) is stated from the plan, not assumed.
- [ ] Lock/wait evidence was checked if timeouts or contention were reported.
- [ ] Production-vs-local differences (data volume, stats, config) were considered.
- [ ] Recommendations include write/storage cost, not just read-side benefit.
- [ ] A verification plan exists to confirm the fix before/after.
- [ ] Missing plan, schema, index, or production access is marked pending.
- [ ] No recommendation is based solely on local query timing.
