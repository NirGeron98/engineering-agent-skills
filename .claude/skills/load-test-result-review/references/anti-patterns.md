# Load Test Result Review Anti-Patterns

## Synthetic load as launch proof

- **Symptom**: A passing load test under an unrealistic or under-scaled scenario is treated as sufficient evidence to launch.
- **Why it matters**: A test that doesn't match production workload shape or scale can pass while production still fails.
- **Correction**: Explicitly assess workload realism and environment parity before accepting the result as launch proof.

## Average latency hides tail pain

- **Symptom**: A test is called a "pass" because average latency met the target, while p95/p99 latency or error rate was well outside acceptable bounds.
- **Why it matters**: Tail behavior is often what users and downstream systems actually experience under load.
- **Correction**: Review latency percentiles and error rates, not just averages, before declaring pass/fail.

## Environment parity gap ignored

- **Symptom**: Results from a small-scale or differently configured test environment are extrapolated directly to production capacity.
- **Why it matters**: Non-linear scaling effects (connection limits, cache size, GC behavior) can make small-scale results misleading.
- **Correction**: State environment differences explicitly and qualify conclusions accordingly, or flag as pending until closer parity is achieved.

## Ramp pattern mismatch

- **Symptom**: A test ramps load gradually and smoothly when production traffic is actually bursty or spiky.
- **Why it matters**: Gradual ramps can hide problems that only appear under sudden load spikes (e.g., cold caches, connection storms).
- **Correction**: Match the ramp pattern to the real production traffic shape, or note the mismatch as a limitation.

## Saturation point unreported

- **Symptom**: The test report states throughput achieved but not where or how the system began to degrade.
- **Why it matters**: Without the saturation point, there's no evidence for how much headroom exists before failure.
- **Correction**: Identify and report the saturation point explicitly from the test data.
