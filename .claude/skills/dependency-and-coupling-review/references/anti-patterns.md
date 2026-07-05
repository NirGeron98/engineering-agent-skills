# Dependency and Coupling Anti-patterns

## Coupling hidden behind interfaces

- **Symptom**: Interfaces are added but the same upstream package still changes for downstream reasons.
- **Why it matters**: The dependency shape looks cleaner while blast radius remains.
- **Correction**: Verify dependency direction, ownership, and change frequency improve.

## Shared-utils gravity

- **Symptom**: More domains depend on a growing `utils`, `common`, or `shared` package.
- **Why it matters**: Shared packages turn local changes into broad rebuilds and reviews.
- **Correction**: Split by domain responsibility or move behavior to the owning module.

## Framework leakage

- **Symptom**: Domain modules import web, ORM, UI, cloud, or queue framework types.
- **Why it matters**: Domain changes become coupled to infrastructure and test setup.
- **Correction**: Move framework specifics to adapters or boundary layers when evidence shows real pain.

## Graph theater

- **Symptom**: The output includes a graph but no ranked risk or action.
- **Why it matters**: Visualization alone does not improve architecture.
- **Correction**: Rank edges by blast radius and propose concrete verification.

## Ban-list architecture

- **Symptom**: The recommendation is "never import X from Y" without migration or exceptions.
- **Why it matters**: Teams need usable paths, not slogans.
- **Correction**: Define allowed directions, transition steps, and enforcement checks.
