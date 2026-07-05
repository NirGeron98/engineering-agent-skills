# Module Boundary Review Anti-patterns

## Abstraction by taste

- **Symptom**: Code is moved because the new location feels cleaner.
- **Why it matters**: Boundary churn without evidence increases migration risk.
- **Correction**: Tie each move to ownership, dependency direction, testability, or change-safety evidence.

## Interface paint

- **Symptom**: A new interface is added while the same domain knowledge and lifecycle coupling remain.
- **Why it matters**: It hides coupling instead of reducing it.
- **Correction**: Check whether dependency direction, ownership, and blast radius actually change.

## Shared-utils gravity

- **Symptom**: Shared utilities become the place for domain behavior that nobody owns.
- **Why it matters**: Domains become coupled through "neutral" helpers.
- **Correction**: Move domain behavior back to an owning module or split utilities by responsibility.

## Boundary big bang

- **Symptom**: The proposed fix splits modules, updates callers, rewrites tests, and changes deployment in one step.
- **Why it matters**: Failures become hard to isolate.
- **Correction**: Sequence compatibility-preserving slices with verification after each step.

## Component review drift

- **Symptom**: The review spends most effort on UI component props or styling.
- **Why it matters**: That belongs to component design review, not module boundaries.
- **Correction**: Hand off to `.claude/skills/component-design-review`.
