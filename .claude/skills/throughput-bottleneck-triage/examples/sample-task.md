# Sample Task

## User prompt

"Our service cannot process more than 300 jobs/min even after adding workers."

## Abbreviated run

- Ask for CPU/memory/IO/network/DB utilization and queue depth during the ceiling.
- Note that throughput not improving with added workers suggests a shared downstream bottleneck (e.g., DB connection limit or a single-threaded dependency).
- Obstacle: DB connection pool limits and downstream dependency concurrency settings are not yet known.

## Evidence requests

- DB connection pool size/limit and utilization during the throughput ceiling.
- Any configured concurrency limits (thread pool, semaphore, rate limiter) in the job processing path.
- Queue depth trend during and after the worker-scaling attempt.

## Expected output

A throughput bottleneck triage with a hypothesis pointing to a shared downstream limit (pending confirmation), an evidence table showing utilization per layer, safe experiments (e.g., temporarily raising the DB pool limit in a test environment) to confirm the hypothesis, and scaling/optimization options once confirmed — explicitly not recommending further worker scaling until the shared bottleneck is identified.

## Why this passes the quality bar

The agent does not recommend scaling further as a first response given that scaling has already plateaued, and it ties the hypothesis to a specific layer's evidence rather than a generic capacity guess.
