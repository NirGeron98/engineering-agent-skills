# Sample Task

## User prompt

"Debugging this service is hard because logs are noisy and missing request context. Review log signal quality."

## Abbreviated run

- Pull recent log samples for the service, including a known failure case.
- Identify repetitive low-value lines and check whether requests carry a correlation/trace ID through the log lines.
- Obstacle: the sample only covers one code path; other endpoints' logging behavior is unknown.

## Evidence requests

- Log samples from at least one additional endpoint/path for comparison.
- The logging library/middleware configuration (to check correlation ID propagation setup).

## Expected output

A review identifying specific noisy log lines, a missing correlation-ID gap confirmed for the sampled path (with other paths marked pending), and concrete recommended changes (add request ID to log context, remove/lower level for the noisy lines) with before/after examples.

## Why this passes the quality bar

The agent bases findings on the actual sampled logs, does not generalize to unseen endpoints, and gives specific rather than generic logging advice.
