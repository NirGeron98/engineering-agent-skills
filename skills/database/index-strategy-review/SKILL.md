---
name: index-strategy-review
description: Review whether existing or proposed database indexes match actual query patterns, cardinality, and read/write tradeoffs. Use when index design across multiple queries, composite index order, or index bloat/cost needs review. Produces an index strategy review with recommended indexes, rejected indexes with reasons, and a verification plan.
---

# Index Strategy Review

## Goal

Turn a set of query patterns and existing/proposed indexes into an evidence-backed index strategy that balances read benefit against write/storage cost.

## When to use

- Indexes need review against the actual, important query patterns of a system.
- Composite index column order, uniqueness, or partial-index design is in question.
- Index bloat, redundancy, or write-cost concerns are raised.

## When not to use

- A single slow query needs diagnosis; use `skills/database/query-performance-review`.
- A live migration's safety (locks, backfill) is the concern, not index design itself; use `skills/database/schema-migration-safety-review`.
- The bottleneck is broader than indexing (CPU, IO, concurrency); use `skills/performance/throughput-bottleneck-triage`.

## Composition and handoff rules

- Defer slow single-query diagnosis to `skills/database/query-performance-review`.
- Defer live migration safety to `skills/database/schema-migration-safety-review`.
- Defer broad performance triage to `skills/performance/throughput-bottleneck-triage`.

## Evidence access rule

Inspect the actual query patterns (real queries or query logs), existing index definitions, table cardinality/selectivity, and write volume. If query patterns, current indexes, or cardinality data are unavailable, request exact query samples or schema/index dumps and mark recommendation conclusions pending.

## Workflow

1. Collect the actual query patterns this index strategy must serve (real queries, not assumed access patterns).
2. Inventory existing indexes and check which queries they do or do not serve.
3. Assess cardinality/selectivity of candidate columns and read/write tradeoffs of proposed indexes.
4. Decide composite index column order and whether unique/partial indexes apply.
5. Recommend indexes to add, indexes to drop (redundant/unused/bloated), and indexes to reject with reasons.
6. Produce a safe creation/verification plan (e.g., concurrent/online index build, confirm plan uses new index).
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to index every column or skip write-cost analysis.

## Questions to ask (only when necessary)

- What are the top N queries by frequency or cost that this index strategy must serve?
- What is the acceptable write-latency/storage budget for new indexes?

## Quality bar

- Every recommended index is justified by a specific, real query pattern.
- Rejected indexes have a stated reason (low selectivity, redundant, write cost too high).
- Write/storage cost is stated for every recommendation, not just read-side benefit.

## Expected output format

```markdown
## Index strategy review: <scope>

**Recommended indexes**: <index definitions, justified by query pattern>
**Rejected indexes**: <index, reason>
**Write/storage cost notes**: <details>
**Verification plan**: <steps>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Index everything**: proposing an index for every filterable/sortable column without justification.
- **Cardinality guessing**: assuming a column is selective without checking actual distribution.
- **Write-cost blindness**: recommending indexes without noting their impact on write throughput/storage.

More in `references/anti-patterns.md`.
