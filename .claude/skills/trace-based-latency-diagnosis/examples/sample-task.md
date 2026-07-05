# Sample Task

## User prompt

"We have distributed traces for a slow checkout request. Diagnose where latency is coming from."

## Abbreviated run

- Inspect the trace: span list, durations, and parent/child relationships across the checkout request path.
- Identify the span or gap consuming the most time and check for unexplained gaps between spans.
- Obstacle: a large unexplained gap appears between the payment-service span and the next span, with no child span for the actual payment gateway call.

## Evidence requests

- Whether the payment gateway call is instrumented; if not, request logs/timing from that call directly.
- A baseline trace from a fast checkout request for comparison.

## Expected output

A latency breakdown showing the payment-service gap as the largest unexplained time, a bottleneck hypothesis pointing to the uninstrumented payment gateway call marked pending until direct evidence is available, ruled-out causes for the other spans checked, and a recommendation to add instrumentation around the gateway call.

## Why this passes the quality bar

The agent does not treat the unexplained gap as resolved and does not guess a cause without span or direct timing evidence.
