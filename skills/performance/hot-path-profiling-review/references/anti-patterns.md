# Hot Path Profiling Review Anti-Patterns

## Profile-free optimization

- **Symptom**: Optimization suggestions are made from intuition ("this loop looks slow") without any profiling evidence.
- **Why it matters**: Intuition about hot spots is frequently wrong; real bottlenecks are often elsewhere.
- **Correction**: Require an actual profile before proposing optimizations; mark conclusions pending without one.

## Average latency hides tail pain

- **Symptom**: A hot path is declared "fixed" based on average latency improving while p95/p99 remain bad.
- **Why it matters**: Tail latency often drives the worst user experience and cascading timeouts.
- **Correction**: Check percentile/tail latency, not just averages, before declaring success.

## Explain plan theater (profiling variant)

- **Symptom**: A profile's flame graph shape is eyeballed without checking actual sample counts or time percentages per function.
- **Why it matters**: Visual impressions of a flame graph can mislead; small-looking frames can still dominate cumulative time.
- **Correction**: Use actual sample/time percentages, not visual impression, to rank hot functions.

## Measurement-environment mismatch

- **Symptom**: A profile captured under low local load is used to justify a production optimization decision.
- **Why it matters**: CPU/IO/lock behavior changes significantly with concurrency and hardware.
- **Correction**: Profile under representative load/hardware, or explicitly flag the mismatch as a limitation.

## Micro-optimization without critical-path check

- **Symptom**: A hot function is optimized without confirming it's actually on the request's critical path.
- **Why it matters**: Optimizing an incidental or parallel-executed function doesn't improve end-to-end latency.
- **Correction**: Confirm the function is on the critical path before investing in optimizing it.
