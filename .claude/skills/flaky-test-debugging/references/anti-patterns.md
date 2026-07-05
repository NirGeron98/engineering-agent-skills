# Flaky Test Debugging Anti-Patterns

## Retry as fix

- **Symptom**: The answer increases retries and stops.
- **Why it matters**: CI looks greener while the defect still exists.
- **Correction**: Treat retries as temporary containment with owner and expiration.

## Single-green proof

- **Symptom**: One passing run is used to declare repair.
- **Why it matters**: Intermittent failures need repeated evidence.
- **Correction**: Run a stress loop or repeated CI/local jobs under the failing conditions.

## Timeout inflation

- **Symptom**: Timeouts are increased without cause.
- **Why it matters**: Slow failures become slower and still flaky.
- **Correction**: Identify the timing dependency, wait condition, resource contention, or external service.

## Order blindness

- **Symptom**: The test is run alone and declared fixed.
- **Why it matters**: The failure may depend on prior tests or shared state.
- **Correction**: Reproduce with randomized order, previous test subset, or full shard context.

