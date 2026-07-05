# Legacy Characterization Tests Anti-Patterns

## Refactor before capture

- **Symptom**: The plan starts by moving code or changing structure.
- **Why it matters**: Behavior changes become impossible to distinguish from refactor noise.
- **Correction**: Capture current behavior at stable seams first.

## Snapshot sprawl

- **Symptom**: Large unstable outputs are snapshotted without intent.
- **Why it matters**: Reviews become unreadable and tests are either brittle or ignored.
- **Correction**: Snapshot only meaningful stable output, and name what it protects.

## Bug laundering

- **Symptom**: Suspicious behavior is silently preserved or silently fixed.
- **Why it matters**: Compatibility and correctness decisions get hidden in tests.
- **Correction**: Label known bugs, compatibility quirks, and intentional changes separately.

## Seam fantasy

- **Symptom**: Tests are planned at seams that do not exist or require a rewrite first.
- **Why it matters**: The test plan cannot be executed safely.
- **Correction**: Choose existing public seams or propose a tiny harness seam with risk noted.

