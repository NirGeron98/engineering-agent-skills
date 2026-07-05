# Data Consistency Incident Diagnosis Checklist

- [ ] The symptom is stated precisely: affected records/entities, stores involved, and since when.
- [ ] The declared source of truth was confirmed, not assumed.
- [ ] The write path from source of truth to affected store was traced.
- [ ] The read path and any replication/cache/event flow were traced.
- [ ] Recent deployments, jobs, or schema changes were checked as possible triggers.
- [ ] Idempotency/retry behavior on the write path was considered.
- [ ] Known facts and suspected cause are clearly separated.
- [ ] Affected scope is labeled confirmed vs estimated, not asserted as complete.
- [ ] The reconciliation plan avoids a blind mass-update before scope is confirmed.
- [ ] Missing records, path code, or replication evidence is marked pending.
