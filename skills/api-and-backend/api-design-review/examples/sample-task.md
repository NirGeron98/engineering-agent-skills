# Sample task: review a new bulk-invite endpoint

## Prompt

> Review the API design in PR #88: `POST /api/orgs/:orgId/invites/bulk` — accepts a list of emails, invites them all.

## Abbreviated agent run

1. Convention baseline read: `POST /api/orgs/:orgId/members`, `GET /api/orgs/:orgId/invites`, error envelope `{error: {code, message, fields?}}`, camelCase, cursor pagination, `requireOrgRole('admin')` guard pattern.
2. Consumers: web app only (found single caller scaffolded in the frontend repo reference).
3. Request: `{emails: string[]}` — no max length, no per-email format validation (verified: handler maps straight to `sendInvite`).
4. Response: returns `207`-style mixed results but as a bare array — deviates from the service envelope; per-item failures use raw strings, not `code`s.
5. Authz: route registered without `requireOrgRole` — verified in `invites.route.ts`; every sibling write route has it.
6. Idempotency: re-posting the same list re-sends emails; existing single-invite endpoint dedupes by (orgId, email) — inconsistent.

## Expected output

```markdown
## API design review: POST /api/orgs/:orgId/invites/bulk

**Consumers**: internal web app only
**Convention baseline**: orgs/members POST, orgs/invites GET (invites.route.ts, members.route.ts)

**Contract-breaking / blocking**
1. No authz guard on the route — any authenticated user can bulk-invite to any
   org — suggest: requireOrgRole('admin') like every sibling write route
   (invites.route.ts:14 vs members.route.ts:9)
2. No bound on emails[] — one request can trigger unbounded email sends —
   suggest: max 100 per request, 422 with field error beyond

**Operational gaps**
- Re-POST re-sends all invites; single-invite endpoint dedupes by (orgId,
  email) — mirror that dedupe for consistency and retry safety
- No per-email format validation before hitting the mail service

**Consistency with service conventions**
- Response is a bare array; service convention is {data, ...} envelope with
  {error: {code, ...}} per failure (convention: members.route.ts, error-handler.ts)
- Per-item failure reasons are free-text strings; convention is machine codes

**Compatibility classification**
- New endpoint — all additive; safe to iterate before frontend merges

**Checked and clean**
- Naming and URL shape match existing invite resources
- Timestamps in response are RFC 3339 UTC like the rest of the API
```

## Why this passes the quality bar

Every consistency finding cites the existing endpoint that sets the convention; the authz gap was verified in the route code rather than trusted from the PR description; limits and idempotency are treated as contract, not implementation detail.
