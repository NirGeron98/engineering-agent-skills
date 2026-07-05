# Auth Flow Review — Checklist

## Model & inventory
- [ ] Intended access matrix written: principals × resources × operations
- [ ] Every route in scope enumerated (not sampled), with its authn + authz guard
- [ ] GraphQL: resolvers and mutations inventoried (and sensitive fields), not just the HTTP route
- [ ] Internal / service-to-service endpoints inventoried: authenticated, or trusting the network?
- [ ] Unguarded routes confirmed intentionally public or flagged

## Authentication
- [ ] Token verification checks signature, expiry, audience/issuer (read the verify options)
- [ ] Refresh rotation; revocation on logout / password change / role downgrade
- [ ] Reset & invite tokens: single-use, expiring
- [ ] No user enumeration via error messages or timing
- [ ] Login / password-reset endpoints rate-limited or lockout-protected (brute force)
- [ ] Cookie-based sessions: CSRF protection present (token or SameSite policy verified)

## Object-level authorization
- [ ] Every ID-parameter route: ownership/tenant predicate traced to the actual query
- [ ] Every list endpoint: scoping predicate present
- [ ] Nested resources: parent ownership also verified

## Privilege escalation
- [ ] Mass assignment: role/orgId/isAdmin not settable via request bodies (read the create/update calls)
- [ ] Batch / import / webhook / internal endpoints have the same guards as CRUD routes
- [ ] Admin checks enforced server-side, not only in UI
- [ ] Email/identity changes re-verified where identity grants access

## Failure behavior & audit
- [ ] Expired/invalid token → 401; missing permission → 403; no 500s
- [ ] Existence leakage considered (404 vs 403 policy consistent)
- [ ] Authz denials logged with actor + resource; no tokens/secrets in logs

## Verification
- [ ] Each gap confirmed across all inspectable layers (middleware, handler, query, DB policy)
- [ ] Uninspectable layers (gateway, RLS) named in the report
- [ ] Regression authz test written or specified per confirmed gap
