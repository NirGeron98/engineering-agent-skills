# Auth Flow Review — Anti-patterns

## "It has requireAuth, it's fine"
**Symptom**: route review stops at the authentication middleware.
**Why it fails**: authenticated user A fetching `/documents/123` owned by user B is the single most common real-world access-control bug (IDOR / BOLA).
**Instead**: for every ID-taking route, trace to the query and find the ownership/tenant predicate.

## Sampled inventory
**Symptom**: the five prominent CRUD routes get reviewed; the CSV-import endpoint, the webhook receiver, and the "temporary" debug route do not.
**Instead**: enumerate routes mechanically from the router/framework; review the table, not your memory.

## Gap declared, layer missed
**Symptom**: "no tenant check in the handler!" — but a Postgres RLS policy enforces it.
**Instead**: check every enforcement layer you can see; explicitly name layers you can't inspect instead of guessing in either direction.

## Valid-token-only testing
**Symptom**: auth "verified" by confirming a good token works.
**Instead**: exercise expired, wrong-audience, tampered, and revoked tokens; read the verify call's options — flags like `ignoreExpiration: true` hide in plain sight.

## Mass-assignment blindness
**Symptom**: `User.update(req.body)` assumed safe because the UI never sends `role`.
**Instead**: the API contract is what the server accepts, not what the UI sends; verify field allow-listing at the handler/ORM layer.

## UI as the security boundary
**Symptom**: admin menu hidden for non-admins; the admin API endpoints themselves check nothing.
**Instead**: every server-side operation enforces its own authorization, regardless of what the UI exposes.

## Stale claims in long-lived tokens
**Symptom**: role and tenant are embedded in the JWT at issuance with a 24h expiry; an admin demoted at 09:00 keeps admin power until tomorrow.
**Why it fails**: authorization was decided once, at login — the token is a cache of claims with no invalidation story.
**Instead**: either re-check authz-critical claims server-side per request, keep token lifetimes short with rotation, or maintain a revocation/refresh path that fires on role change. Verify which of these the code actually does.

## 404/403 inconsistency
**Symptom**: unauthorized access to an existing resource returns 403, missing ones 404 — letting attackers enumerate what exists.
**Instead**: pick a policy (usually 404 for both on private resources) and apply it consistently.

## Findings without regression tests
**Symptom**: the IDOR is fixed today and reintroduced in the next refactor.
**Instead**: every confirmed gap ships with a test that fails if the guard disappears.
