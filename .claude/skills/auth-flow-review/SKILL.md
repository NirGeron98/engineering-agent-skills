---
name: auth-flow-review
description: Review authentication and authorization flows for gaps — missing route guards, broken object-level access control, token lifecycle flaws, privilege escalation paths — by tracing enforcement in the actual code. Use when endpoints or resources are added/changed, roles or permissions are introduced, login/session/token code is touched, or someone asks "is this properly protected". Produces a verified access-control findings report per endpoint and resource. Defensive review only.
---

# Auth Flow Review

## Goal

Verify that every route, resource, and operation in scope enforces the intended authentication and authorization — in code, at the right layer — and report every gap with a concrete unauthorized-access scenario. This is defensive review of systems you're authorized to work on.

## When to use

- New endpoints, resources, or admin surfaces added.
- Roles, permissions, tenancy, or sharing semantics introduced or changed.
- Login, session, token issuance/refresh, or password-reset code touched.
- Periodic review: "is our API properly locked down?"

## When not to use

- Full penetration testing or attacking systems you don't own/operate.
- Pure authn *implementation* from scratch (design task; this skill reviews).
- Infrastructure security (network policy, IAM for cloud resources) — different domain.
- General implementation review of a diff where access control is only one concern among many — use `code-review`, which hands the auth-specific depth back here.
- Contract-shape questions (naming, pagination, error format) — that's `api-design-review`.

## Workflow

1. **Map the intended access model first.** From code, docs, and route structure: who are the principals (anonymous, user, org member, admin, service), what are the resources, and what may each principal do to each resource? Write the matrix down — gaps are found by comparing reality against it.
2. **Inventory every route in scope and its guard.** Enumerate routes mechanically (router files, decorators, OpenAPI) — don't sample. In GraphQL, the unit of inventory is the resolver/mutation (and field-level access where sensitive), not the single HTTP route. For each: which authn middleware and which authz check apply? Build the table. Routes with no guard are findings unless intentionally public — confirm intent, don't assume. Include internal/service-to-service endpoints: "only reachable from inside the network" is a topology claim, not an authz check.
3. **Verify authentication mechanics.**
   - Token validation: signature, expiry, audience/issuer actually checked (read the verify call and its options — `verify` with `ignoreExpiration` or missing audience is a classic).
   - Session/token lifecycle: expiry sane, refresh rotation, revocation on logout/password change/role downgrade.
   - Credential flows: password reset and invite tokens single-use and expiring; no user enumeration via error/timing differences.
4. **Verify object-level authorization (the highest-yield check).** For every route with an ID parameter: does the handler check the object belongs to the caller's user/org, or does it fetch by ID alone? Trace the query — `WHERE id = $1` without a tenant/owner predicate is an IDOR. Check list endpoints for the scoping predicate too.
5. **Hunt privilege escalation paths.**
   - Mass assignment: can a request body set `role`, `orgId`, `isAdmin`?
   - Self-service edits to authz-relevant fields (email change without re-verification where email grants access).
   - Admin checks done client-side or only in the UI layer.
   - Indirect writes: batch/import/webhook endpoints bypassing the guards the CRUD routes have.
6. **Check the failure and edge behavior.** Expired token → 401 not 500; missing permission → 403 without leaking resource existence where that matters; authz errors logged with actor and resource (audit trail); no secrets/tokens in logs.
7. **Verify by tracing, and test where possible.** For each suspected gap, follow the exact request path in code to confirm the check is truly absent (it may live in a gateway, a decorator, or a DB row-level policy — look at all layers before declaring a gap). Where a test harness exists, write the failing authz test.
8. **Report** per the output format, with the access matrix, per-route table, and findings ranked by impact. Recommend an authz test for every confirmed gap. Before reporting, load `references/checklist.md` and confirm every section — it contains checks (CSRF, rate limiting, service-to-service) not spelled out above; consult `references/anti-patterns.md` before declaring any gap.

## Questions to ask (only when necessary)

- The *intended* access model, when code offers no evidence (is cross-org read meant to be allowed for support staff?).
- Whether enforcement intentionally lives outside the app (API gateway, RLS) when you can't see those configs — ask rather than assume either way.

## Quality bar

- Route inventory is exhaustive for the scope, not sampled.
- Every "unprotected" finding verified across all enforcement layers visible to you (middleware, handler, query predicate, DB policy) — with layers you couldn't inspect named explicitly.
- Every finding has a concrete scenario: which principal performs which request and what they wrongly obtain.
- Object-level checks traced to the actual query/predicate, not inferred from middleware presence.
- Intended-vs-actual matrix included so "correctly open" and "wrongly open" are distinguishable.

## Expected output format

```markdown
## Auth flow review: <scope>

**Access model** (intended)
| Resource | anonymous | user | org admin | service |
|---|---|---|---|---|

**Route inventory**: <N routes enumerated; source: <router files>>

**Findings** (ranked)
1. <gap> — <route/file:line> — scenario: <principal + request → wrongly obtains X>
   — verified at layers: <middleware/handler/query/DB> — suggest: <fix + test>

**Token & session lifecycle**
- <expiry/rotation/revocation findings or "verified sane">

**Checked and clean**
- <routes/flows verified correct, incl. which layer enforces them>

**Layers not inspectable**
- <gateway/RLS/etc. you could not verify, if any>
```

## Failure modes to avoid

- **Middleware = safe**: seeing `requireAuth` on a route and skipping the object-level check — authn without object-level authz is where most real breaches live.
- **Sampling the routes**: reviewing the five obvious endpoints and missing the batch-import route with no guard.
- **Declaring gaps without checking every layer**: reporting a missing check that actually lives in the gateway or a DB row-level policy — verify each layer or name it as uninspected.

More in `references/anti-patterns.md`.
