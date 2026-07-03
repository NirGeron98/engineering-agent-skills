---
name: memory-leak-diagnosis
description: Diagnose unexpected memory growth, high GC pressure, OOM-killed processes, or long-running service degradation using memory metrics and heap/profile evidence. Use when a suspected memory leak needs a retention-path diagnosis rather than a generic restart. Produces a memory leak diagnosis with a suspected retention path, ruled-out causes, and a mitigation/verification plan.
---

# Memory Leak Diagnosis

## Goal

Turn unexplained memory growth into an evidence-backed retention-path diagnosis and a safe mitigation and verification plan.

## When to use

- Memory grows over time until a process is OOM-killed or restarted.
- GC pressure is unusually high, or heap profiles show retained objects that shouldn't persist.
- A long-running service degrades gradually and a leak is suspected.

## When not to use

- The question is a Kubernetes/pod-level OOM policy or resource limit issue, not the application's retention path; use `skills/devops-infrastructure/kubernetes-workload-debugging`.
- The issue is a general service bug unrelated to memory; use `skills/api-and-backend/service-debugging`.
- The central question is whether to add/remove caching, not diagnosing a leak; use `skills/performance/caching-strategy-review`.

## Composition and handoff rules

- Defer Kubernetes OOM/pod-level diagnosis to `skills/devops-infrastructure/kubernetes-workload-debugging`.
- Defer general service debugging to `skills/api-and-backend/service-debugging`.
- Defer cache design to `skills/performance/caching-strategy-review` when caching is central.

## Evidence access rule

Inspect actual memory metrics over time, heap/profile snapshots (ideally two points in time to diff retained objects), and the code paths for caches, queues, timers, and listeners. If memory metrics or heap profiles are unavailable, request exact commands/tools to capture them and mark retention-path conclusions pending.

## Workflow

1. Confirm the growth pattern from memory metrics: linear, stepped, or workload-correlated, and time to OOM/degradation.
2. Obtain heap/profile snapshots (ideally two points in time) and diff retained object types/counts.
3. Trace suspected retention paths: caches without eviction, queues that grow unbounded, timers/listeners not cleaned up, or object lifecycle bugs.
4. Correlate growth with workload pattern (specific requests, jobs, or traffic shape) to help reproduce it.
5. Rule out causes not supported by the evidence, and state the suspected retention path with reasoning.
6. Produce a mitigation plan (fix retention path, add eviction/limits, bound growth) and a verification loop (repeat measurement after fix).
7. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to recommend a restart/scale-up as the fix or skip a heap diff.

## Questions to ask (only when necessary)

- Can I get two heap/profile snapshots at different points in the growth curve to diff?
- Is there a specific workload pattern (job type, endpoint, traffic shape) correlated with the growth?

## Quality bar

- The suspected retention path is tied to an actual heap diff or profile evidence, not guessed from symptoms.
- Ruled-out causes are stated explicitly, not silently dropped.
- The mitigation plan fixes the retention path rather than just adding memory/restart frequency.

## Expected output format

```markdown
## Memory leak diagnosis: <scope>

**Evidence table**: <memory metrics, heap diff, growth pattern>
**Suspected retention path**: <finding and reasoning>
**Ruled-out causes**: <candidate, reason ruled out>
**Mitigation plan**: <steps>
**Verification loop**: <steps>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Restart-as-fix**: recommending scheduled restarts/scale-up instead of diagnosing the retention path.
- **Leak claim without heap evidence**: proposing a leak cause without a heap/profile diff to support it.
- **Cache as magic**: assuming a cache can't be the leak source without checking its eviction/bound behavior.

More in `references/anti-patterns.md`.
