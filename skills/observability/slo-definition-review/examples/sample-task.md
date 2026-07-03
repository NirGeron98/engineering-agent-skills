# Sample Task

## User prompt

"Define practical SLIs/SLOs for this API based on user experience."

## Abbreviated run

- Identify the API's critical user journey (e.g., checkout completion) and what latency/error rate would feel broken to users.
- Look for existing metrics/traffic data (request latency histograms, error rates) to ground threshold proposals.
- Obstacle: only 1 week of traffic data is available, and it includes an unusual traffic spike from a marketing event.

## Evidence requests

- A longer traffic history window excluding or separately marking known anomalous periods.
- Any existing user-facing latency complaints or support tickets tied to this API.

## Expected output

An SLO proposal with SLIs tied to the checkout journey, thresholds derived from the available week of data but explicitly marked as a starting hypothesis pending a longer, anomaly-excluded dataset, and an error-budget policy with a named owner.

## Why this passes the quality bar

The agent does not copy an industry-standard number and flags the limited/anomalous data window instead of treating the one-week sample as fully representative.
