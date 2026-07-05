# Connection Pool and Locking Debugging Anti-Patterns

## Timeout as fix

- **Symptom**: Connection or query timeouts are increased to make errors "go away" without diagnosing the cause.
- **Why it matters**: This delays failure rather than preventing it and can worsen resource exhaustion under load.
- **Correction**: Diagnose the underlying cause (leak, long transaction, contention) before changing timeouts.

## Pool-size-as-fix

- **Symptom**: The connection pool size is increased whenever exhaustion is reported.
- **Why it matters**: If the cause is a connection leak or long-held transaction, a bigger pool just delays the same failure at higher cost to the database.
- **Correction**: Confirm whether connections are leaking or held too long before resizing the pool.

## Local reproduction assumption

- **Symptom**: The issue is judged "not reproducible" because it doesn't occur locally under low concurrency.
- **Why it matters**: Lock contention and pool exhaustion are concurrency-dependent and often only appear under production load.
- **Correction**: Use production metrics/logs, not local reproduction attempts, to judge concurrency-dependent issues.

## Lock evidence skipped

- **Symptom**: A deadlock or contention issue is diagnosed from application logs alone without reading database lock/deadlock logs.
- **Why it matters**: Application logs often show the symptom (timeout) but not the actual lock ordering or contention path.
- **Correction**: Read actual database-side lock/deadlock evidence before concluding a root cause.

## Explain plan theater (transaction variant)

- **Symptom**: A long transaction is assumed short because its individual queries are fast, ignoring total transaction duration and held locks.
- **Why it matters**: A transaction holding locks across multiple round-trips can cause contention even if each query is fast.
- **Correction**: Measure total transaction duration and lock hold time, not just individual query time.
