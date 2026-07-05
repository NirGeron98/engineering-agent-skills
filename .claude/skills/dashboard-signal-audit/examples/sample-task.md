# Sample Task

## User prompt

"Review this operational dashboard. It has many graphs, but on-call says it is not useful during incidents."

## Abbreviated run

- Inventory the dashboard's panels, their queries, and chart types.
- Classify each panel as actionable or vanity based on what on-call has reported needing during recent incidents.
- Obstacle: no incident postmortem or usage log confirms which panels (if any) were actually consulted during the last incident.

## Evidence requests

- The most recent incident timeline or postmortem noting which signals were checked.
- On-call's specific complaint: what question they couldn't answer using this dashboard.

## Expected output

An audit classifying each panel as actionable or vanity based on available evidence, missing panels tied to the failure mode on-call couldn't diagnose, an incident-usefulness score marked untested/pending until postmortem data is available, and specific recommended panel changes.

## Why this passes the quality bar

The agent does not assign a confident usefulness score without incident evidence and ties missing-panel findings to the specific reported gap rather than generic dashboard advice.
