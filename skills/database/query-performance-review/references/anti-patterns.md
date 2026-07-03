# Query Performance Review Anti-Patterns

## Explain plan theater

- **Symptom**: The plan is read for its shape (joins, scan types) without comparing estimated vs actual rows.
- **Why it matters**: Misestimation is the most common root cause of bad plan choices; skipping it misses the real story.
- **Correction**: Always compare estimated vs actual rows at each node before concluding.

## Index everything

- **Symptom**: A new index is proposed for every filter/sort column without checking write cost or redundancy.
- **Why it matters**: Excess indexes slow writes, bloat storage, and can go unused.
- **Correction**: Justify each proposed index against actual query patterns and note its write cost.

## Local query timing as truth

- **Symptom**: A query is judged fast or slow based on local execution time alone.
- **Why it matters**: Local data volume, stats, caching, and hardware differ from production.
- **Correction**: Get production plan/timing evidence before concluding.

## Timeout as fix

- **Symptom**: A query timeout is raised to "fix" a slow query instead of diagnosing the cause.
- **Why it matters**: This hides the problem and risks resource exhaustion elsewhere.
- **Correction**: Diagnose the bottleneck; only use timeout changes as a stopgap with a named follow-up.

## Average latency hides tail pain

- **Symptom**: A query is called "fine" based on average latency while p95/p99 timeouts continue.
- **Why it matters**: Tail latency is often what causes user-visible failures and cascading timeouts.
- **Correction**: Review percentile/tail evidence, not just averages, when available.
