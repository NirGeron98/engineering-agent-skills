# Sample Task

## User prompt

"Review these load test results and tell us whether we are ready to launch."

## Abbreviated run

- Ask for the test scenario (workload mix, ramp pattern), environment details, and raw results (latency percentiles, error rates).
- Note that the report only mentions average latency and total throughput achieved.
- Obstacle: latency percentile breakdown and environment-parity details are not yet provided.

## Evidence requests

- p95/p99 latency and error rate data from the test, not just averages.
- Confirmation of test environment scale/config relative to production.
- The workload mix and ramp pattern used, compared to real production traffic shape.

## Expected output

A load test result review stating validity concerns (percentile and parity data missing), a go/no-go recommendation held pending until percentile and environment-parity evidence is supplied, and proposed follow-up tests (percentile capture, closer environment parity, and a burst-ramp scenario) rather than an unqualified "ready to launch" verdict.

## Why this passes the quality bar

The agent does not accept an average-latency-only report as sufficient evidence and does not issue a go recommendation without percentile and parity data.
