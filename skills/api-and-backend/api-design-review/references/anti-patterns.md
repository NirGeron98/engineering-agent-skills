# API Design Review — Anti-patterns

## Textbook over neighborhood
**Symptom**: demanding snake_case and RFC 7807 errors in a service where 40 endpoints consistently use camelCase and a custom error envelope.
**Why it fails**: one "correct" endpoint in an otherwise consistent API is a net loss for every consumer.
**Instead**: the service's convention is the standard; propose convention changes as a separate, service-wide discussion.

## Convention-worship of a bad baseline
**Symptom**: the service consistently returns 200 with an error body, so the review demands the new endpoint do it too — propagating a harmful pattern in the name of consistency.
**Why it fails**: consistency arguments are for style and shape; they don't launder practices that break clients, monitoring, or caching.
**Instead**: flag both — the deviation (for consistency) and the baseline itself (as a separate, service-wide finding). Don't silently propagate harm.

## The 200-only review
**Symptom**: request/response of the success case polished; nobody asked what a validation failure, an empty list, or a conflict returns.
**Instead**: review the error and edge shapes with the same rigor as the happy path.

## Paper authz
**Symptom**: "requires admin role" appears in the OpenAPI description; the route registers no guard.
**Instead**: locate the middleware/decorator/guard in code for every authz claim.

## The semantic rug-pull
**Symptom**: `amount` silently changes from net to gross. Same name, same type, all clients now wrong — and no tool catches it.
**Instead**: explicitly ask of every retained field: did its meaning change? Meaning changes are breaking changes.

## "We'll paginate later"
**Symptom**: collection endpoint ships unbounded because current data is small.
**Why it fails**: adding pagination changes the response shape — a breaking change on your busiest endpoint, later.
**Instead**: paginate growable collections from day one, even with a generous default page size.

## Stringly-typed errors
**Symptom**: clients told to match on `message: "User not found"`.
**Instead**: stable machine-readable `code` field; messages are for humans and may change freely.

## Retry roulette
**Symptom**: a payment-creating POST with no idempotency mechanism; every client timeout is a potential double charge.
**Instead**: idempotency keys (or naturally idempotent semantics) on any write a client might retry.

## Review-as-redesign
**Symptom**: asked to review `POST /exports`, the review proposes remodeling the whole resource hierarchy.
**Instead**: findings scoped to the contract under review; larger remodels flagged as separate work.
