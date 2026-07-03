# API Docs Review Anti-Patterns

## Spec worship

- **Symptom**: Generated docs are treated as correct without checking source or implementation.
- **Why it matters**: Generated artifacts can be stale or incomplete.
- **Correction**: Compare docs, schemas, handlers, tests, and examples.

## Happy-path docs only

- **Symptom**: Only 200 responses are documented.
- **Why it matters**: Integrators fail on auth, validation, pagination, limits, or error semantics.
- **Correction**: Include realistic errors and edge cases.

## Example drift

- **Symptom**: Request or response examples do not match schemas or implementation.
- **Why it matters**: SDKs and consumers copy broken examples.
- **Correction**: Validate examples against tests, schemas, or live/generated output.

## Design rewrite disguised as docs

- **Symptom**: The review redesigns endpoints rather than documenting them.
- **Why it matters**: Contract changes need explicit design review.
- **Correction**: Hand design concerns to `skills/api-and-backend/api-design-review`.

