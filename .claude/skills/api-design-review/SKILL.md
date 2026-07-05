---
name: api-design-review
description: Review a new or changed HTTP/RPC API contract for consistency, evolvability, error semantics, pagination, idempotency, and backward compatibility before implementation or merge. Use when an endpoint is being added or modified, an OpenAPI/proto/GraphQL schema changes, or someone asks "does this API design look right". Produces a contract-level findings report grounded in the service's existing API conventions — not generic REST lecturing.
---

# API Design Review

## Goal

Catch contract mistakes while they're still cheap — before clients depend on them. Judge the API against the service's own established conventions first, general API design principles second, and produce findings a developer can apply directly.

## When to use

- A new endpoint, RPC, or GraphQL field is proposed or implemented.
- Request/response shapes, status codes, or error formats change.
- An OpenAPI/proto/schema diff appears in a PR.
- Someone asks whether an API is "RESTful enough", versioned right, or safe to evolve.

## When not to use

- Internal function signatures with no external consumers.
- Implementation bugs behind a stable contract (use `code-review` or `service-debugging`).
- The question is access control depth — who may call what, object-level ownership, token lifecycle — defer to `auth-flow-review`; here, verify enforcement exists and hand off.
- Big-picture API strategy (versioning policy for the whole platform) — that's an architecture discussion, not a contract review.

## Workflow

1. **Inventory the existing conventions before judging anything.** Read 3–5 existing endpoints in the same service: naming (camelCase vs snake_case), envelope shape, error format, pagination style, auth mechanism, versioning. The service's convention beats textbook preference — flag deviations from *it* first. For non-REST contracts, translate the checks rather than skip them: in GraphQL, errors-as-data replaces status codes and field deprecation replaces versioning; in gRPC, status codes and field numbering carry the compatibility rules.
2. **Establish who consumes the contract.** Internal frontend, mobile apps with slow release cycles, third parties, other services. Consumer type sets the compatibility bar.
3. **Review resource modeling.** Nouns and hierarchy match domain language; IDs opaque and consistently typed; no verbs-as-resources unless the service already does actions that way.
4. **Review the request side.** Validation defined for every field (types, ranges, formats); required vs. optional deliberate; unknown fields policy stated; limits on list sizes and payloads.
5. **Review the response side.** Consistent envelope; every field's nullability intentional; timestamps in one format (prefer RFC 3339 UTC); money/quantities with explicit units; no internal leakage (DB ids where UUIDs are convention, stack traces, internal enums).
6. **Review error semantics.** Correct status code classes; machine-readable error codes, not string-matching on messages; validation errors point at fields; the error shape matches the service's standard one.
7. **Review the operational contract.**
   - Pagination on every collection that can grow — with a stable ordering.
   - Idempotency: retried POSTs/writes — idempotency keys or natural idempotency, stated.
   - Rate-limit and timeout behavior for expensive endpoints.
   - Authn/authz: who may call this, and where is it enforced — verify the enforcement exists in code, not just in the design doc.
8. **Review evolvability and compatibility.** Every change classified: additive (safe) vs. breaking (removed/renamed field, tightened validation, changed semantics of an existing field — the sneakiest one). For breaking changes: migration plan or version bump required.
9. **Report** in the output format, citing the convention source for each consistency finding. Before reporting, load `references/checklist.md` and confirm every contract area was covered; consult `references/anti-patterns.md` when weighing a finding against the service's conventions.

## Questions to ask (only when necessary)

- Who the consumers are and their release cadence, if not discoverable from the codebase.
- Expected collection cardinality when pagination cost is being weighed ("will this list ever exceed ~100 items?").

Conventions, existing shapes, and enforcement points must be found by reading the code, not by asking.

## Quality bar

- Consistency findings cite the specific existing endpoint(s) that establish the convention.
- Every breaking-change claim names the consumer behavior that breaks.
- Authz findings verified against actual middleware/guards in code.
- No generic REST dogma ("should be HATEOAS") disconnected from this service's reality.
- Findings ranked: contract-breaking > operational gaps > consistency > polish.

## Expected output format

```markdown
## API design review: <endpoint(s) / schema>

**Consumers**: <who depends on this contract>
**Convention baseline**: <existing endpoints used as reference>

**Contract-breaking / blocking**
1. <finding> — breaks: <specific consumer behavior> — suggest: <direction>

**Operational gaps**
- <pagination / idempotency / authz / limits finding> (<evidence in code>)

**Consistency with service conventions**
- <deviation> (convention established in <existing endpoint/file>)

**Compatibility classification**
- <each change: additive / breaking + migration note>

**Checked and clean**
- <areas verified without findings>
```

## Failure modes to avoid

- **Textbook-first reviewing**: enforcing your favorite style against a service that consistently does it another way. Consistency with the neighborhood wins.
- **Missing the semantic break**: a field keeps its name and type but changes meaning (net → gross amount) — the worst kind of breaking change; hunt for it explicitly.
- **Trusting the design doc's authz claim**: "admin-only" in the doc, no guard in the code. Verify enforcement.

More in `references/anti-patterns.md`.
