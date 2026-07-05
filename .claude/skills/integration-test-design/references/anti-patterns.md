# Integration Test Design Anti-Patterns

## Mocked integration

- **Symptom**: The test mocks the boundary it claims to verify.
- **Why it matters**: Contract and persistence defects still escape.
- **Correction**: Use a real dependency or contract fixture for the boundary under test.

## Shared-state fixture

- **Symptom**: Tests depend on preexisting mutable data or leave data behind.
- **Why it matters**: Order and parallelism create false failures.
- **Correction**: Generate isolated fixtures and clean them deterministically.

## External service surprise

- **Symptom**: CI tests call real third-party services without controls.
- **Why it matters**: Failures become slow, expensive, and nondeterministic.
- **Correction**: Use controlled fakes, contract tests, or sandbox services with explicit limits.

## No failure diagnostics

- **Symptom**: A failed test only reports timeout or generic assertion failure.
- **Why it matters**: Debugging cost erases the value of the integration test.
- **Correction**: Capture boundary request/response, DB records, events, or logs.

