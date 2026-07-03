---
name: caching-strategy-review
description: Review whether to add, remove, or change caching for performance, cost, reliability, or consistency reasons, covering cache purpose, key design, TTL, invalidation, stampede risk, and fallback behavior. Use when a caching decision needs a tradeoff review, not a performance profiling exercise. Produces a caching strategy review with a cache fit assessment, risks/tradeoffs, and an invalidation plan.
---

# Caching Strategy Review

## Goal

Turn a proposed or existing caching decision into an evidence-backed fit assessment that weighs performance gain against consistency, reliability, and cost tradeoffs.

## When to use

- A team is deciding whether to add, remove, or change caching for a data path.
- Cache key design, TTL, invalidation, or stampede risk needs review.
- Stale-data or fallback-behavior concerns are raised about an existing cache.

## When not to use

- The reported issue is an active data inconsistency incident, not a caching design question; use `skills/database/data-consistency-incident-diagnosis`.
- The question is which code path to optimize based on profiling evidence, not whether to cache; use `skills/performance/hot-path-profiling-review`.
- The decision is a broad system architecture tradeoff beyond caching itself; use `skills/architecture/architecture-review`.

## Composition and handoff rules

- Defer data consistency incidents to `skills/database/data-consistency-incident-diagnosis`.
- Defer hot path profiling to `skills/performance/hot-path-profiling-review`.
- Defer architecture-level tradeoffs to `skills/architecture/architecture-review`.

## Evidence access rule

Inspect the actual data path, its read/write ratio, freshness requirements, and (for existing caches) key design, TTL, and invalidation code. If read/write ratio, freshness requirements, or existing cache configuration are unavailable, request exact metrics/code and mark fit-assessment conclusions pending.

## Workflow

1. Confirm the cache's intended purpose (reduce latency, reduce load, reduce cost) and the data's actual freshness requirement.
2. Assess read/write ratio and access pattern to judge whether caching fits this data path at all.
3. Review cache key design, TTL, and invalidation strategy (explicit invalidation vs TTL-only) for correctness.
4. Check stampede risk (many concurrent misses on expiry) and fallback behavior if the cache is unavailable.
5. Weigh consistency tradeoffs against performance/cost gain, and produce a fit assessment (add/keep/remove/change).
6. Produce an invalidation plan and observability recommendations (cache hit rate, staleness monitoring) plus a verification plan.
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to treat caching as a default performance fix or ignore stampede/fallback risk.

## Questions to ask (only when necessary)

- What staleness window is acceptable for this data from the business/user perspective?
- What is the read/write ratio and request volume for this data path?

## Quality bar

- The fit assessment is based on actual read/write ratio and freshness requirements, not "caching generally helps."
- Stampede risk and fallback behavior (cache down/unavailable) are explicitly addressed.
- Invalidation strategy is concrete (explicit invalidation events, TTL value with reasoning), not left vague.

## Expected output format

```markdown
## Caching strategy review: <scope>

**Cache fit assessment**: <add/keep/remove/change, with reasoning>
**Risks and tradeoffs**: <consistency, stampede, fallback>
**Invalidation plan**: <steps>
**Verification plan**: <steps>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Cache as magic**: assuming caching is safe/beneficial by default without checking freshness requirements or access pattern.
- **Stampede blindness**: no plan for many concurrent misses when a hot key expires.
- **Fallback assumption**: assuming the system behaves safely when the cache is unavailable without checking the fallback path.

More in `references/anti-patterns.md`.
