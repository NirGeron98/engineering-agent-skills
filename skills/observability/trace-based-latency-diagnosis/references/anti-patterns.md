# Trace-Based Latency Diagnosis Anti-Patterns

## Trace as truth without missing-span caveat

- **Symptom**: A request's total latency is attributed entirely to visible spans, ignoring an unexplained gap that likely hides an uninstrumented call.
- **Why it matters**: Missing instrumentation can hide the real bottleneck (e.g., a downstream call with no child span).
- **Correction**: Explicitly flag unexplained gaps as missing-instrumentation risk, not settled.

## Single-trace generalization

- **Symptom**: A conclusion is drawn from one slow trace without checking whether it's representative of the general pattern.
- **Why it matters**: One trace could be an outlier (cold cache, GC pause, one-off retry).
- **Correction**: Compare multiple traces or explicitly note the conclusion is single-sample and needs confirmation.

## Symptom-based guessing

- **Symptom**: A bottleneck is named (e.g., "probably the database") based on the request type rather than span evidence.
- **Why it matters**: This skips the actual diagnostic value of tracing.
- **Correction**: Only name a bottleneck that is backed by an actual span duration.

## Vanity metrics as reliability

- **Symptom**: Average latency is used to declare the request "fine" while p95/p99 or specific slow traces show a real problem.
- **Why it matters**: Averages hide tail latency that affects real users.
- **Correction**: Look at the specific slow trace(s) in question, not just aggregate averages.

## Baseline-free comparison

- **Symptom**: A trace is called "slow" without any fast/baseline trace to compare against.
- **Why it matters**: Without a baseline, it's unclear which spans are actually abnormal.
- **Correction**: Request or gather a baseline trace for the same request type when available.
