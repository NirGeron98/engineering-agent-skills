# Test Strategy Anti-Patterns

## Coverage theater

- **Symptom**: The plan targets a coverage percentage without naming critical behavior.
- **Why it matters**: High coverage can miss the failure modes users actually care about.
- **Correction**: Prioritize tests by risk, impact, and current coverage gaps.

## E2E everything

- **Symptom**: Most behavior is pushed into browser or full-stack flows.
- **Why it matters**: The suite becomes slow, brittle, and hard to diagnose.
- **Correction**: Place checks at the lowest layer that proves the behavior.

## Test everything

- **Symptom**: The strategy lists every possible edge case without tradeoffs.
- **Why it matters**: Teams ignore plans that are too expensive to maintain.
- **Correction**: Include "not worth testing yet" with clear reasons.

## Missing evidence

- **Symptom**: Recommendations are based on intuition rather than code, CI, incidents, or contracts.
- **Why it matters**: The plan may optimize for imaginary risks.
- **Correction**: Request exact files, test output, CI timings, specs, or production context.

## Strategy without an owner

- **Symptom**: The plan lists risks and tests but never assigns who writes, runs, or is accountable for verifying them.
- **Why it matters**: An unowned plan becomes a wish list that nobody executes.
- **Correction**: Assign an owner or verification responsibility to each recommended test or risk item.

