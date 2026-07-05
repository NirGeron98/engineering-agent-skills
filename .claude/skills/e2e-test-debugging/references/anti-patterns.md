# E2E Test Debugging Anti-Patterns

## Sleep as diagnosis

- **Symptom**: The proposed fix is an arbitrary delay.
- **Why it matters**: It hides race conditions and slows the suite.
- **Correction**: Wait for user-visible state, network completion, or app event with evidence.

## Screenshot-only certainty

- **Symptom**: The diagnosis comes from a single screenshot.
- **Why it matters**: Visual state may not reveal DOM, network, console, or timing causes.
- **Correction**: Pair visual evidence with trace, DOM, console, network, or logs.

## Retry hides flake

- **Symptom**: A retry is treated as a successful fix.
- **Why it matters**: Users still face the underlying broken state.
- **Correction**: Use retries only as temporary containment with owner and expiry.

## Selector archaeology

- **Symptom**: The fix chases brittle selectors without checking the product behavior.
- **Why it matters**: The test may be right and the app may be wrong.
- **Correction**: Compare expected user flow against actual browser evidence.

