---
name: query-performance-review
description: Review a slow, expensive, unstable, or timing-out database query using its execution plan, row estimates vs actuals, index usage, and lock/wait evidence. Use when a specific query needs a performance diagnosis and safe query/index/schema recommendations. Produces a query performance review with an evidence table, likely bottleneck, and a verification plan.
---

# Query Performance Review

## Goal

Turn a slow or unstable query into an evidence-backed diagnosis and a safe, verifiable fix plan.

## When to use

- A specific query is slow, expensive, timing out, or causing resource pressure.
- Production and local performance for the same query diverge.
- A query needs its execution plan, index usage, or row estimates reviewed before changing it.

## When not to use

- The bottleneck is a general backend hot path, not a specific query; use `.claude/skills/hot-path-profiling-review`.
- Index design across multiple query patterns is the actual question; use `.claude/skills/index-strategy-review`.
- The workload is a data pipeline or warehouse query; use `.claude/skills/db-to-dwh-pipeline` or the relevant `skills/data/` workflow.
- Trace evidence (not the query plan) is the primary input; use `.claude/skills/trace-based-latency-diagnosis`.

## Composition and handoff rules

- Defer broad backend runtime performance to `.claude/skills/hot-path-profiling-review`.
- Defer index-specific strategy to `.claude/skills/index-strategy-review` when index design across queries becomes central.
- Defer data pipeline/warehouse optimization to `.claude/skills/db-to-dwh-pipeline` or relevant data skills.
- Defer trace-based latency diagnosis to `.claude/skills/trace-based-latency-diagnosis` when trace evidence is the primary input.

## Evidence access rule

Inspect the actual query text, its execution plan (e.g., `EXPLAIN ANALYZE` or equivalent), row estimates vs actuals, index definitions, and any lock/wait evidence. If the plan, schema, indexes, or production-vs-local diff is unavailable, request the exact command output needed and mark bottleneck and recommendation conclusions pending.

## Workflow

1. Capture the query text and its context: frequency, caller, expected data volume, and what "slow" means (latency target vs actual).
2. Obtain and read the execution plan; compare estimated vs actual rows at each node to find misestimation.
3. Check index usage: which indexes are used, which are missing, whether joins/filters/sorts force scans.
4. Check for lock/wait evidence and production-vs-local differences (data volume, stats freshness, config).
5. Identify the likely bottleneck and produce recommended query/index/schema changes with risks.
6. Produce a safe verification plan (re-run plan, compare before/after, check for regressions on other queries).
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to trust local timing, treat the plan as theater, or recommend an index without checking write cost.

## Questions to ask (only when necessary)

- What is the acceptable latency target for this query?
- Can I get an `EXPLAIN ANALYZE` (or equivalent) from production, not just local?

## Quality bar

- The bottleneck claim is traced to specific plan nodes or lock evidence, not guessed from query shape alone.
- Recommendations state write/storage cost tradeoffs, not just read-side benefit.
- A verification plan exists before the fix is considered done.

## Expected output format

```markdown
## Query performance review: <query/scope>

**Evidence table**: <plan nodes, estimated vs actual rows, indexes used/missing, locks/waits>
**Likely bottleneck**: <finding>
**Recommended changes**: <query/index/schema>
**Risks**: <write cost, storage, behavior change>
**Verification plan**: <steps>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Local-timing trust**: treating local query timing as representative of production behavior.
- **Explain-plan theater**: reading the plan's shape without checking estimated-vs-actual row counts.
- **Index-without-cost recommendation**: proposing an index without noting its write/storage cost.

More in `references/anti-patterns.md`.
