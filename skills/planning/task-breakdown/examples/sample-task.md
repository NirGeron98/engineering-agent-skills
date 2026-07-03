# Sample task: plan "add soft-delete for projects"

## Prompt

> Add soft-delete for projects in our API. Deleted projects should be hidden everywhere but restorable by admins.

## Abbreviated agent run

1. Outcome: "DELETE /projects/:id marks a project deleted; deleted projects vanish from all list/get endpoints; POST /admin/projects/:id/restore brings them back."
2. Investigated: `src/models/project.ts` (Prisma), 7 query sites found via `grep -rn "prisma.project"`, existing soft-delete precedent found on `Comment` model (`deletedAt` + default scope helper `notDeleted()`).
3. Contract change: `deletedAt DateTime?` column; existing `DELETE` endpoint changes semantics (hard → soft) — flagged as a risk.

## Expected output

```markdown
## Plan: soft-delete for projects with admin restore

**Pattern to mirror**: Comment soft-delete — deletedAt column + notDeleted() helper
(src/models/comment.ts, src/db/scopes.ts)

**Steps**
1. Migration: add nullable `deletedAt` to Project — files: prisma/schema.prisma,
   new migration — verify: `prisma migrate dev` + existing tests green
2. Route DELETE /projects/:id to set deletedAt instead of deleting — files:
   src/routes/projects.route.ts, src/services/projects.service.ts — verify:
   update projects.service.spec.ts delete tests
3. Apply notDeleted() to all 7 project query sites — files: (7 paths listed) —
   verify: new spec "deleted projects hidden from list/get"
4. Add POST /admin/projects/:id/restore behind requireAdmin — files:
   src/routes/admin.route.ts, projects.service.ts — verify: new admin spec incl.
   403 for non-admin
5. Backfill audit: log delete/restore via existing auditLog() — files:
   projects.service.ts — verify: assert audit entries in specs from steps 2 & 4

**Risks**
- DELETE semantics change silently for API consumers → accepted; response code
  stays 204, restore covers mistakes
- Query sites added later may forget notDeleted() → mitigated: spec in step 3
  asserts count of unscoped call sites via lint rule already used for Comment

**Out of scope**
- Cascade soft-delete of project tasks (needs product decision)
- Purge job for old deleted projects

**Open questions**
- Should project names of deleted projects stay reserved (unique constraint)?
```

## Why this passes the quality bar

Every file was located by search before planning; an existing repo pattern anchors the design; the schema change precedes dependent steps; each step carries its own verification; the semantic change to DELETE is surfaced as a risk instead of hidden.
