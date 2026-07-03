# Sample Task

## User prompt

"This backend endpoint is slow. We have a CPU profile and request traces."

## Abbreviated run

- Read the CPU profile; identify the top functions by cumulative time percentage.
- Cross-check against the request trace to confirm those functions are on the critical path, not parallel/incidental work.
- Obstacle: the measurement load level (concurrency during profiling) is not stated.

## Evidence requests

- The load level/concurrency present when the CPU profile was captured.
- The latency target for this endpoint (p50/p95/p99 if available).

## Expected output

A hot path profiling review with bottleneck evidence citing specific functions and their time share from the profile, optimization candidates ranked by evidence strength, at least one rejected guess (a function that looked suspicious but wasn't on the critical path per the trace), and a before/after verification plan using the same profiling method — with load-level-dependent conclusions marked pending until confirmed.

## Why this passes the quality bar

The agent ties every candidate to actual profile data and trace evidence rather than guessing, and it does not declare success without a same-method before/after comparison.
