# SLO Definition Review Anti-Patterns

## SLO copied from another service

- **Symptom**: A target like "99.9% availability" is proposed because it's a common industry default, without checking this service's traffic or user tolerance.
- **Why it matters**: Copied targets can be too loose (masking real problems) or too strict (wasting engineering effort).
- **Correction**: Derive thresholds from this service's actual historical data and user impact.

## Vanity metrics as reliability

- **Symptom**: An SLI is chosen because it's easy to measure and looks good, not because it reflects real user experience.
- **Why it matters**: A service can hit its SLO while users still experience failures the SLI doesn't capture.
- **Correction**: Choose SLIs that map directly to the user journey's success/failure definition.

## Unowned error budget

- **Symptom**: An error budget is defined with no policy for what happens when it's exhausted.
- **Why it matters**: Without a consequence, the budget is symbolic and doesn't influence prioritization.
- **Correction**: Define and assign ownership for the budget-exhaustion policy (freeze features, prioritize reliability work).

## Round-number threshold

- **Symptom**: A threshold is chosen because it's a clean number (99%, 95%) rather than derived from data.
- **Why it matters**: Clean numbers rarely match actual traffic/latency distributions.
- **Correction**: Base the threshold on percentile analysis of real historical data.

## Missing evidence

- **Symptom**: SLO recommendations are made without checking whether traffic/latency data actually exists or is accessible.
- **Why it matters**: Unfounded thresholds may not hold under real load patterns.
- **Correction**: Request exact data sources and mark thresholds as hypotheses until validated.
