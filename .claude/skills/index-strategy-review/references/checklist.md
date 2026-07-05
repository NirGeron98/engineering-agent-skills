# Index Strategy Review Checklist

- [ ] Actual query patterns (real queries or logs) were collected, not assumed.
- [ ] Existing indexes were inventoried and checked against those query patterns.
- [ ] Cardinality/selectivity of candidate columns was checked, not assumed.
- [ ] Composite index column order is justified by query filter/sort order.
- [ ] Each recommended index is tied to a specific query pattern.
- [ ] Each rejected index has a stated reason.
- [ ] Write/storage cost is noted for every recommended index.
- [ ] A safe creation plan (e.g., online/concurrent build) is included.
- [ ] A verification step confirms the query planner actually uses the new index.
- [ ] Missing query samples, index definitions, or cardinality data are marked pending.
