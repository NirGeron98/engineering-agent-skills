# Sample Task

## User prompt

"Our worker memory grows over several hours until it gets OOM-killed."

## Abbreviated run

- Ask for memory metrics over the growth window and any heap snapshots taken.
- Identify candidate retention paths: an in-process cache without eviction, or an unbounded queue.
- Obstacle: no heap snapshot diff exists yet, only memory metric graphs.

## Evidence requests

- Two heap/profile snapshots at different points in the growth window (e.g., 1 hour in and 5 hours in).
- The code for any in-process caches, queues, or long-lived collections used by this worker.
- Whether growth correlates with a specific job type or traffic pattern.

## Expected output

A memory leak diagnosis stating the growth pattern from the metrics (linear/stepped), a suspected retention path marked pending until a heap diff is available, ruled-out causes if any evidence excludes them, a mitigation plan once the path is confirmed, and a verification loop over a multi-hour window matching the original leak's timescale.

## Why this passes the quality bar

The agent does not recommend a restart schedule as the fix, does not name a specific retention path without heap-diff evidence, and verifies over a realistic timescale rather than a short synthetic test.
