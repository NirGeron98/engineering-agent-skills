# Metrics and Alerts Review Anti-Patterns

## Alert noise accepted as normal

- **Symptom**: Frequently firing alerts with no action taken are left uncommented in the review.
- **Why it matters**: Alert fatigue causes real incidents to be missed.
- **Correction**: Flag high-frequency, low-action alerts explicitly and recommend tuning or removal.

## Vanity metrics as reliability

- **Symptom**: Metrics that look good (uptime percentage, request count) are used to claim reliability without connecting to user-facing failure modes.
- **Why it matters**: A metric can look healthy while users experience failures.
- **Correction**: Tie reviewed metrics to actual user-impacting failure modes.

## Threshold guessing

- **Symptom**: A new alert threshold is proposed without checking historical metric ranges.
- **Why it matters**: Guessed thresholds cause either noise or missed detection.
- **Correction**: Base thresholds on historical distribution and known incident values.

## Remediation without ownership

- **Symptom**: Missing-coverage or noise findings are listed with no owner to implement changes.
- **Why it matters**: Findings without an owner don't get fixed.
- **Correction**: Assign an owner for each alert change.

## Coverage assumed

- **Symptom**: A failure mode is assumed to be covered because "there's probably an alert for that."
- **Why it matters**: Assumed coverage can leave real gaps undetected until an incident occurs.
- **Correction**: Verify the alert actually exists and fires correctly before claiming coverage.
