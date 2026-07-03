# Memory Leak Diagnosis Anti-Patterns

## Restart as fix

- **Symptom**: A scheduled restart or process recycling policy is proposed as the resolution for a leak.
- **Why it matters**: This hides the underlying bug and risks worse failures as the leak grows faster over time or under higher load.
- **Correction**: Diagnose and fix the retention path; use restarts only as a temporary stopgap with a named follow-up.

## Leak claim without heap evidence

- **Symptom**: A retention path is guessed ("probably the cache") without a heap diff or profile to confirm it.
- **Why it matters**: Guessed causes waste effort and can leave the real leak unresolved.
- **Correction**: Require a heap/profile diff before concluding a retention path.

## Cache as magic

- **Symptom**: A cache is assumed safe from leaking because "caches are supposed to be bounded."
- **Why it matters**: Unbounded or misconfigured caches are one of the most common leak sources.
- **Correction**: Check actual eviction policy, size bound, and TTL configuration of every cache in the retention path.

## Synthetic load as launch proof (leak variant)

- **Symptom**: A fix is verified only under a short synthetic test and declared resolved.
- **Why it matters**: Leaks often only manifest after hours of realistic workload; short synthetic tests can miss slow leaks.
- **Correction**: Verify over a duration and workload shape representative of when the leak actually appears.

## Average latency hides tail pain (memory variant)

- **Symptom**: Average memory usage looks stable while peak/tail memory usage trends upward and periodically triggers OOM kills.
- **Why it matters**: Averages can mask a slow leak that only matters at the tail before a crash.
- **Correction**: Track peak and trend, not just average memory usage, over the measurement window.
