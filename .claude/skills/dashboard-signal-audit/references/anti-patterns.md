# Dashboard Signal Audit Anti-Patterns

## Dashboard as wallpaper

- **Symptom**: Panels are present and look informative but no one references them during an actual incident.
- **Why it matters**: A dashboard that isn't used during incidents provides false confidence in observability.
- **Correction**: Classify each panel by whether it actually drives an on-call decision.

## Vanity metrics as reliability

- **Symptom**: Metrics like total request count or uptime percentage are prominent while user-impacting error/latency signals are buried or missing.
- **Why it matters**: Vanity metrics can look healthy while real problems are invisible.
- **Correction**: Prioritize panels tied to user-impacting failure modes.

## Panel-count-as-coverage

- **Symptom**: A dashboard with many panels is assumed to have good incident coverage.
- **Why it matters**: Quantity doesn't guarantee the right failure modes are visualized.
- **Correction**: Check coverage against a list of known critical failure modes, not panel count.

## Untested usefulness claim

- **Symptom**: An incident-usefulness score is given without checking whether the dashboard was actually used during a real incident.
- **Why it matters**: A confident-sounding score without evidence is misleading.
- **Correction**: Mark the score as an untested hypothesis when no real incident usage evidence exists.

## Misleading default view

- **Symptom**: A panel's default time range or aggregation hides short spikes relevant to incidents.
- **Why it matters**: On-call may miss the exact signal they need during a live incident.
- **Correction**: Check default time range/aggregation against what's actually needed during triage.
