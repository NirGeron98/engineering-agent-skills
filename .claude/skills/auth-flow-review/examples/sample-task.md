# Sample task: review access control on a documents API

## Prompt

> We just added sharing to our documents feature (owners can share docs with other users as viewer/editor). Review the auth flow for the documents endpoints.

## Abbreviated agent run

1. Access model derived from code + product spec: owner (full), editor (read/update), viewer (read), unrelated user (nothing), anonymous (nothing).
2. Route inventory from `documents.route.ts` + `shares.route.ts`: 9 routes. All have `requireAuth`. Guard table built.
3. Object-level trace: `GET /documents/:id` → `docService.get(id, userId)` → predicate checks owner **or** share — correct. `PATCH /documents/:id` → same — correct. But `GET /documents/:id/versions` → `versionsRepo.list(docId)` — **no predicate**; verified no gateway/RLS layer exists (searched infra config in repo).
4. Share manipulation: `POST /documents/:id/shares` checks ownership — correct. `DELETE /shares/:shareId` fetches share by ID alone — any authenticated user can delete any share (verified query).
5. Mass assignment: `PATCH /documents/:id` passes `req.body` to Prisma update; `ownerId` is therefore settable — ownership transfer by editors. Verified schema has no field allow-list.
6. Token lifecycle: JWT verify includes expiry + audience (checked options); refresh rotation present; revocation on password change **absent** (noted, lower severity).
7. Wrote failing authz tests for findings 1–3 in the existing supertest harness.

## Expected output

```markdown
## Auth flow review: documents + sharing endpoints

**Access model** (intended)
| Resource | anon | unrelated user | viewer | editor | owner |
|---|---|---|---|---|---|
| document read | – | – | ✓ | ✓ | ✓ |
| document update | – | – | – | ✓ | ✓ |
| shares manage | – | – | – | – | ✓ |

**Route inventory**: 9 routes (documents.route.ts, shares.route.ts) — table in appendix

**Findings** (ranked)
1. Version history readable by any authenticated user —
   documents.route.ts:74 → versionsRepo.list has no owner/share predicate —
   scenario: user with no access GETs /documents/123/versions and reads full
   content history — verified: handler + query; no gateway/RLS in repo —
   suggest: reuse docService access check; test added (versions.authz.spec.ts)
2. Any user can delete any share — shares.route.ts:41 fetches by shareId only —
   scenario: attacker enumerates shareIds, revokes a victim org's sharing —
   suggest: verify caller owns the parent document; test added
3. Ownership transfer via mass assignment — documents.service.ts:88 passes
   req.body to update; editor PATCHes {ownerId: self} and becomes owner —
   suggest: explicit field allow-list; test added

**Token & session lifecycle**
- Verify checks signature/expiry/audience (auth.ts:22) — sane
- No token revocation on password change — recommended follow-up

**Checked and clean**
- GET/PATCH/DELETE /documents/:id — owner-or-share predicate verified in query
- POST /documents/:id/shares — ownership check verified
- No user enumeration in login/reset flows (uniform errors, checked)

**Layers not inspectable**
- None — no API gateway or DB RLS in this stack (verified in infra config)
```

## Why this passes the quality bar

All nine routes were enumerated, not sampled; every gap was traced to the query level and checked against other enforcement layers before being declared; each finding carries a concrete unauthorized-access scenario and shipped with a failing regression test.
