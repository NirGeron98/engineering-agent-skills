# Throughput Bottleneck Triage Anti-Patterns

## Throughput by scaling only

- **Symptom**: Every throughput problem is "fixed" by adding more workers/instances without checking what's actually saturated.
- **Why it matters**: If a shared downstream resource (DB, single-threaded dependency, lock) is the bottleneck, scaling callers just shifts pressure without raising throughput.
- **Correction**: Confirm which layer is saturated before recommending scaling.

## Utilization theater

- **Symptom**: A dashboard is glanced at and a bottleneck is declared without reading actual utilization numbers.
- **Why it matters**: Visual impressions of graphs can miss the actual saturated resource, especially with misleading scales or aggregation.
- **Correction**: Read actual utilization/queue-depth numbers at each layer before concluding.

## Synthetic load as launch proof

- **Symptom**: A throughput fix is verified against a synthetic load test with a different shape (e.g., uniform requests) than the real production traffic (bursty, mixed job types).
- **Why it matters**: Bottlenecks can be shape-dependent; a fix validated under the wrong load shape may not hold in production.
- **Correction**: Verify against production-representative load shape, or explicitly flag the gap.

## Average latency hides tail pain (throughput variant)

- **Symptom**: Average job processing time looks fine while queue depth grows unbounded during bursts.
- **Why it matters**: Sustained throughput ceilings are often invisible in averages but visible in growing queue depth.
- **Correction**: Track queue depth/backlog trend, not just average processing time.

## External-limit blindness

- **Symptom**: A throughput ceiling is attributed only to internal saturation without checking downstream/external service limits, quotas, rate limits, or retry/error amplification.
- **Why it matters**: A third-party rate limit or a retry storm can produce the exact same "adding workers didn't help" symptom as internal saturation, but requires a completely different fix.
- **Correction**: Check external service/API limits, downstream quotas, and error/retry rates before concluding the bottleneck is purely internal.

## Concurrency-limit blindness

- **Symptom**: A bottleneck is attributed to raw CPU/IO without checking artificial concurrency limits (thread pool size, connection limits, semaphores).
- **Why it matters**: Many throughput ceilings are caused by a configured limit far below actual hardware capacity.
- **Correction**: Check configured concurrency limits at each layer before attributing the ceiling to hardware saturation.
