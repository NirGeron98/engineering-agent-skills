# Architecture Review Anti-patterns

## Architecture astronomy without repo evidence

- **Symptom**: The review discusses ideal services, layers, or patterns without citing the actual repo, docs, interfaces, or runtime evidence.
- **Why it matters**: It produces plausible but unusable advice.
- **Correction**: Inspect concrete evidence first or request it and mark claims pending.

## Diagram-driven certainty

- **Symptom**: The diagram is treated as the system of record.
- **Why it matters**: Diagrams often lag code and deployment reality.
- **Correction**: Cross-check diagrams against code, interfaces, dependency edges, deployment notes, and owners.

## Rewrite as strategy

- **Symptom**: A rewrite is recommended before incremental alternatives are examined.
- **Why it matters**: Rewrites hide migration risk and delay feedback.
- **Correction**: Compare incremental options, compatibility adapters, and strangler paths before recommending replacement.

## Security/privacy handwave

- **Symptom**: The review says "security looks fine" or "privacy risk is low" without evidence.
- **Why it matters**: Architecture reviews can spot broad risk but are not full security audits.
- **Correction**: Name high-level implications and hand detailed review to the appropriate specialist when needed.

## Tradeoff-free recommendation

- **Symptom**: A preferred architecture is presented as strictly better.
- **Why it matters**: Architecture decisions usually move cost between operability, delivery speed, coupling, and scalability.
- **Correction**: State what gets better, what gets worse, and which constraint makes the tradeoff acceptable.
