---
name: hot-path-profiling-review
description: Review a measurably slow backend or system hot path using profiling output (CPU, allocation, or wall-clock) to find real bottlenecks and safe optimization candidates. Use when a specific request/code path is slow and profiling evidence exists or is needed. Produces a hot path profiling review with bottleneck evidence, optimization candidates, and a before/after verification plan.
---

# Hot Path Profiling Review

## Goal

Turn a slow backend hot path into an evidence-backed bottleneck diagnosis and a safe, verifiable optimization plan.

## When to use

- A specific backend/system hot path is measurably slower than its latency target.
- Profiling output (CPU, allocation, wall-clock) exists or can be captured for the path.
- Guesses about "what's slow" need to be checked against real profiling evidence.

## When not to use

- The bottleneck is clearly a specific database query, not general code path time; use `skills/database/query-performance-review`.
- Only trace evidence exists and profiling is not the tool in play; use `skills/observability/trace-based-latency-diagnosis`.
- The slowness is in frontend rendering/load, not backend; use `skills/frontend/frontend-performance-review`.

## Composition and handoff rules

- Defer database-specific bottlenecks to `skills/database/query-performance-review`.
- Defer trace-only diagnosis to `skills/observability/trace-based-latency-diagnosis`.
- Defer frontend performance to `skills/frontend/frontend-performance-review`.

## Evidence access rule

Inspect actual profiling output (CPU/allocation/wall-clock profile) for the hot path, the request path code, and the measurement environment (load level, hardware, build). If a profile is unavailable, request exact profiling commands/tools to capture one and mark bottleneck conclusions pending.

## Workflow

1. Confirm the latency target and the actual measured latency for this hot path.
2. Obtain and read profiling output; identify hot functions and classify time as CPU, IO, DB, or network-bound.
3. Check allocation hotspots if memory pressure or GC time appears in the profile.
4. Map hot functions back to the actual request path code to confirm they are on the critical path, not incidental.
5. Propose optimization candidates ranked by evidence strength, and state rejected guesses with reasons.
6. Produce a before/after verification plan (same profiling method, same load level) to confirm improvement.
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to optimize without a profile or trust average latency alone.

## Questions to ask (only when necessary)

- What is the latency target, and under what load level was the profile captured?
- Can I get a CPU or allocation profile from a representative (ideally production-like) environment?

## Quality bar

- Every optimization candidate is tied to a specific hot function or allocation site in the profile.
- CPU vs IO vs DB vs network is classified from evidence, not assumed.
- A verification plan uses the same measurement method as the original diagnosis.

## Expected output format

```markdown
## Hot path profiling review: <path>

**Bottleneck evidence**: <hot functions, CPU/IO/DB/network classification>
**Optimization candidates**: <ranked, with evidence>
**Rejected guesses**: <candidate, reason rejected>
**Verification plan**: <before/after method>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Profile-free optimization**: proposing optimizations based on intuition without a captured profile.
- **Average latency hides tail pain**: judging the path "fixed" from average latency while tail latency remains bad.
- **Measurement-environment mismatch**: profiling under unrepresentative load/hardware and generalizing to production.

More in `references/anti-patterns.md`.
