# Sample task: onboarding to an unfamiliar API service

## Prompt

> I just cloned `orders-service`. I need to add a new endpoint later today — first, get oriented.

## Abbreviated agent run

1. Read `README.md` (claims: Express app, `npm start`), no `CLAUDE.md`.
2. Read `package.json` — actually **Fastify 4**, scripts: `dev`, `test` (vitest), `db:migrate`. README is stale.
3. Entry point traced: `src/server.ts` → registers plugins from `src/plugins/`, routes from `src/routes/*.route.ts`.
4. Opened `src/routes/orders.route.ts`, `src/services/orders.service.ts` — route/service/repository layering; zod schemas per route; errors thrown as `AppError` and mapped in `src/plugins/error-handler.ts`.
5. Recent files show tests colocated as `*.spec.ts` next to source, using a `buildTestApp()` helper.
6. Ran `npm test` — 84 passing, 12s. Baseline recorded.
7. Risk: `src/db/migrations/` (Knex), and `src/legacy/exports.ts` marked `@deprecated` but imported by 9 files.
8. No `CLAUDE.md` exists and the repo has a `docs/` folder — **persisted the map to `docs/REPO-MAP.md`**, stamped with today's date and the current commit, so the endpoint work later today (and any future session) starts oriented.

## Expected output

Written to `docs/REPO-MAP.md` (and summarized in chat):

```markdown
## Repo orientation: orders-service (verified 2026-07-02 @ a3f81c2)

**Scope**: whole repo (single service)
**Stack**: Node 20, TypeScript, Fastify 4, Knex + Postgres, vitest (package.json)
**Run/test**: `npm run dev` / `npm test` (verified: 84 passing)

**Entry points**
- src/server.ts — Fastify boot; autoloads src/plugins/ and src/routes/*.route.ts

**Structure**
- src/routes/ — route defs + zod schemas per endpoint (orders.route.ts)
- src/services/ — business logic, throws AppError (orders.service.ts)
- src/db/ — Knex repos + migrations (orders.repo.ts)

**Conventions observed**
- Errors: throw AppError, mapped centrally in plugins/error-handler.ts
- Tests: colocated *.spec.ts using buildTestApp() (orders.service.spec.ts)

**Risk areas / surprises**
- src/legacy/exports.ts is @deprecated but imported by 9 files

**Docs vs. reality**
- README says Express + `npm start`; code is Fastify + `npm run dev`. README is stale.

**Not covered**
- src/plugins/ beyond error-handler; src/db/ migration history
```

## Why this passes the quality bar

Stack came from `package.json`, not the (wrong) README; the doc conflict is reported as a finding; conventions cite real files; the test baseline was actually run; and the map was **persisted to the repo** with a date and commit stamp instead of evaporating with the session — the next agent starts from `docs/REPO-MAP.md`, not from zero.
