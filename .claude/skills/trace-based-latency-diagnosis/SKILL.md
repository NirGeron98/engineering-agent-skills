---
name: trace-based-latency-diagnosis
description: Diagnose a slow request or workflow using distributed traces, spans, and timing breakdowns. Use when a trace evidence table, latency breakdown, bottleneck hypothesis, ruled-out causes, missing span/instrumentation gaps, and next verification steps need to be produced from actual trace data.
---

# Trace-Based Latency Diagnosis

## Goal

Turn actual distributed trace evidence into a latency breakdown and bottleneck hypothesis, without guessing from symptoms alone.

## When to use

- Distributed traces, spans, or timing breakdowns exist (or can be gathered) for a slow request or workflow.
- The user needs to know where latency is actually coming from across a request path.

## When not to use

- The task is backend code performance profiling without trace evidence; defer to future performance skills.
- The task is database query performance specifically; defer to future database/query-performance-review.
- The task is general service runtime debugging without a latency focus; use `.claude/skills/service-debugging`.

## Composition and handoff rules

- Defer backend code performance profiling to future performance skills.
- Defer database query diagnosis to future database/query-performance-review.
- Defer service runtime debugging to `.claude/skills/service-debugging`.

## Evidence access rule

Inspect the actual trace/span data for the slow request (span names, durations, parent/child relationships, service boundaries). If trace data is unavailable or has gaps, request exact trace IDs/exports and mark bottleneck conclusions pending; explicitly note any service/span not instrumented.

## Workflow

1. Gather the actual trace for a representative slow request, including span names, durations, and service/hop boundaries.
2. Build a latency breakdown: which span or gap between spans consumes the most time.
3. Form a bottleneck hypothesis from the breakdown, checking whether the slow span is compute, wait/queue, downstream call, or gap (missing instrumentation).
4. Record ruled-out causes (spans checked and found not to be the bottleneck) rather than only the winning hypothesis.
5. Note missing span/instrumentation gaps that limit confidence (e.g., a downstream call with no child span).
6. Propose next verification steps (add missing instrumentation, compare traces across multiple slow requests, isolate the suspected span).
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to treat a single trace as definitive truth or guess a cause without span evidence.

## Questions to ask (only when necessary)

- Is there a baseline/fast trace for the same request type to compare against?

## Quality bar

- The bottleneck hypothesis is backed by actual span timing, not assumption from the request name.
- Missing-span gaps are called out rather than silently treated as zero-cost.
- Ruled-out causes are recorded, not just the final hypothesis.

## Expected output format

```markdown
## Trace-based latency diagnosis: <request/workflow>

**Trace evidence table**: <span - duration - parent/child>
**Latency breakdown**: <where time goes>
**Bottleneck hypothesis**: <cause and supporting evidence>
**Ruled-out causes**: <span - why ruled out>
**Missing span/instrumentation gaps**: <details>
**Next verification steps**: <what to add/compare>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Trace as truth without missing-span caveat**: treating total request time as fully explained when spans are missing.
- **Single-trace generalization**: drawing a firm conclusion from one trace without checking if it's representative.
- **Symptom-based guessing**: hypothesizing a cause (e.g., "probably the database") without span evidence.

More in `references/anti-patterns.md`.
