# TDD Workflow Anti-Patterns

## Green-first coding

- **Symptom**: Implementation changes appear before a failing test exists.
- **Why it matters**: The test may not prove the bug or feature.
- **Correction**: Write the smallest public-behavior test and observe red first.

## Implementation-detail testing

- **Symptom**: Tests assert private methods, internal state, or mock call counts unrelated to behavior.
- **Why it matters**: Refactors become risky and tests fail for harmless implementation changes.
- **Correction**: Test through public interfaces and observable outcomes.

## Test that cannot fail

- **Symptom**: The test passes before the implementation or asserts only existence.
- **Why it matters**: It does not protect the behavior.
- **Correction**: Prove the test fails for the expected reason before implementation.

## Red reason ignored

- **Symptom**: The test is red because setup, fixture, or import wiring is broken.
- **Why it matters**: The implementation may solve the wrong problem.
- **Correction**: Confirm the failure message matches the behavior gap.

