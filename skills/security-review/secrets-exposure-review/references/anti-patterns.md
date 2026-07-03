# Secrets Exposure Review Anti-Patterns

## Scanner-result absolutism

- **Symptom**: A secret-scanner hit is treated as fully confirmed and scoped without inspection.
- **Why it matters**: Scanners produce false positives and can't tell you real-world exposure duration or visibility.
- **Correction**: Inspect the actual location and history before finalizing severity.

## Remediation without ownership

- **Symptom**: A rotation or revocation step is recommended with no named owner.
- **Why it matters**: Unowned containment steps don't get executed and the exposure lingers.
- **Correction**: Name an owner for each containment and rotation step.

## Broad permission fix

- **Symptom**: A new credential is issued with the same or broader scope as the old one "to be safe."
- **Why it matters**: This repeats or worsens the original risk instead of reducing it.
- **Correction**: Issue the minimum scope needed and document why.

## Containment without evidence preservation

- **Symptom**: The secret is revoked or deleted before exposure scope and history are captured.
- **Why it matters**: Losing the evidence trail prevents accurate severity assessment and incident learning.
- **Correction**: Capture exposure evidence (commit, log, timestamps) before or alongside containment.

## Rotation skipped

- **Symptom**: The finding is "fixed" by removing the secret from the latest code without rotating it.
- **Why it matters**: The credential remains valid and exposed in history.
- **Correction**: Always rotate; removal from the current file alone does not revoke access.
