# Sample task: review a pagination PR

## Prompt

> Review PR #412 — "Add cursor pagination to GET /transactions".

## Abbreviated agent run

1. Intent: replace offset pagination with cursor-based; description promises "backward compatible".
2. Diff shape: `transactions.route.ts`, `transactions.repo.ts`, OpenAPI spec, one new test file. `offset` param handling deleted — flags the compatibility claim.
3. Context read: repo method's callers — an internal export job in `reports/export.ts` still calls with `offset` → now silently ignored, job would loop on page 1 forever. Verified by tracing the call.
4. Boundary hunt: cursor is `base64(created_at)`; two transactions with identical `created_at` → second one skipped across page boundary. Verified against the SQL: `WHERE created_at > $cursor` with no tiebreaker.
5. Tests: new test covers happy path only; no test for duplicate timestamps or invalid cursor (invalid base64 → unhandled 500, verified by reading the decode path).

## Expected output

```markdown
## Review: PR #412 — cursor pagination for GET /transactions

**Verdict**: needs changes

**Must fix**
1. Rows skipped on equal timestamps — transactions.repo.ts:57 — failure: two
   rows share created_at across a page boundary → second never returned —
   suggest: composite cursor (created_at, id) with tiebreaker in WHERE/ORDER BY
2. Internal export job breaks — reports/export.ts:112 still passes `offset`,
   now ignored → infinite loop on page 1 — suggest: migrate the job in this PR
   or keep offset fallback until it's migrated
3. Invalid cursor → 500 — transactions.route.ts:31 — failure: malformed base64
   throws before the error handler maps it — suggest: validate, return 400

**Consider**
- Cursor encodes a raw timestamp — fine, but note clients can forge cursors;
  harmless here since authz scopes the query (checked)

**Test gaps**
- duplicate created_at across pages; invalid cursor; last-page behavior

**Checked and clean**
- Authz unchanged and still applied at route level (transactions.route.ts:18)
- OpenAPI spec updated and matches the implemented response shape
```

## Why this passes the quality bar

Each finding has a location and a concrete failing scenario, verified by tracing (the export-job loop, the SQL tiebreaker, the decode path). The "backward compatible" claim in the description was tested against reality rather than accepted. Clean areas are reported, and no style comments appear.
