# Sample Task

## User prompt

"Our alerts are noisy, but we still miss real incidents. Review metrics and alert quality."

## Abbreviated run

- Inspect alert definitions/thresholds and recent paging history for frequency and outcome (actioned vs ignored).
- Cross-reference recent real incidents against whether an alert fired for them.
- Obstacle: paging history export only covers the last 2 weeks, and one recent major incident is outside that window.

## Evidence requests

- Longer paging/incident history (at least covering the referenced major incident).
- Alert definition export (thresholds, severities, destinations).
- Incident postmortems, if available, noting whether alerts fired.

## Expected output

A review with an alert inventory, signal/noise findings flagging specific noisy alerts, a missing-coverage note marked pending until the older incident's alert history is available, threshold recommendations from the available 2-week data, and a verification plan.

## Why this passes the quality bar

The agent bases noise/coverage findings on actual paging data and does not claim full coverage assessment when older incident data is missing.
