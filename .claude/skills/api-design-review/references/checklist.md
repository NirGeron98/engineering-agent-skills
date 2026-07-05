# API Design Review — Checklist

## Baseline
- [ ] 3–5 existing endpoints read; conventions recorded (naming, envelope, errors, pagination, auth)
- [ ] Consumers identified and compatibility bar set

## Request contract
- [ ] Validation for every field: type, range, format
- [ ] Required vs optional deliberate; unknown-field policy stated
- [ ] Payload and list-size limits defined

## Response contract
- [ ] Envelope matches service convention
- [ ] Nullability of every field intentional and documented
- [ ] One timestamp format (RFC 3339 UTC unless convention differs)
- [ ] Units explicit for money/quantities
- [ ] No internal leakage (DB internals, stack traces, internal enums)

## Errors
- [ ] Status codes semantically correct
- [ ] Machine-readable error codes; field-level validation errors
- [ ] Error shape identical to the service's standard
- [ ] At least one failure case traced or exercised, not just read from the spec

## Operational
- [ ] Pagination on growable collections, with stable ordering
- [ ] Idempotency story for retried writes
- [ ] Authn/authz enforcement verified in code, not just in the doc
- [ ] Rate limits / timeouts considered for expensive endpoints

## Evolution
- [ ] Every change classified additive vs breaking (incl. semantic changes)
- [ ] Breaking changes have a migration plan or version bump
- [ ] OpenAPI/proto/docs updated to match the actual implementation
