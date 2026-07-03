---
name: frontend-state-management
description: Decide where frontend state should live or debug messy/stale state across UI, URL, server cache, forms, optimistic updates, and global stores. Use when a feature has duplicated derived state, stale UI after mutations, race conditions, or unclear local-vs-global-vs-server ownership; produces an evidence-backed state ownership map and fix plan. For API contract design use api-design-review; for proven backend behavior defects use service-debugging.
---

# Frontend State Management

## Goal

Untangle frontend state by assigning each value to the right owner, removing unnecessary duplication, and proving updates, cache invalidation, and race handling behave correctly in the actual project.

## When to use

- UI shows stale, duplicated, or contradictory state.
- A feature mixes local state, URL params, server cache, form state, and global app state without clear ownership.
- Optimistic updates, retries, pagination, filters, or route changes cause races or lost updates.

## When not to use

- Backend endpoint semantics or response design; use `api-design-review`.
- Deployed backend failure, latency, or error spikes; use `service-debugging`.
- Pure form validation behavior; use `form-validation-review`.

## Workflow

**Evidence access rule:** when you cannot run the app, tests, browser, or network inspection yourself, output the exact command or evidence request and mark runtime-dependent conclusions as *pending*. Do not infer live state transitions from code alone when interaction timing matters.

1. **Inventory the state.** From the feature entry point and callers, list each value: local UI, derived, URL, server/cache, global app, form, persistent storage, optimistic/transient. Include where it is read and written.
2. **Identify the source of truth.** For every duplicated value, decide whether it should be derived, stored in the URL, owned by a cache/query layer, owned by a store, or local to a component. Use existing repo conventions before introducing new libraries or stores.
3. **Trace update flows.** Follow create/update/delete/filter/navigation flows through event handlers, effects/watchers, stores, query invalidation, and rendering. General principle first; examples: React hooks/query stores, Vue refs/composables/Pinia, Angular services/signals/RxJS, Svelte stores.
4. **Find race and cache hazards.** Check request cancellation, stale responses, dependency arrays/subscriptions, optimistic rollback, retries, invalidation keys, pagination/filter keys, and unmounted updates.
5. **Propose the smallest ownership change.** Remove duplicated derived state, fix invalidation keys, move state only when multiple real owners need it, and avoid globalizing state for convenience.
6. **Verify behavior.** Prefer focused interaction tests and browser/network evidence. If unavailable, provide exact commands or manual checks and mark results pending.
7. **Write the handoff.** Before writing it, load `references/checklist.md` and verify every item; consult `references/anti-patterns.md` when tempted to add a store, memoize, or mirror data.

## Questions to ask (only when necessary)

- Whether state must survive reload/share links if URL or persistence requirements are not inferable.
- Which stale-state symptom the user actually observed if no reproduction is provided.
- Runtime evidence access when timing or cache behavior cannot be run locally.

## Quality bar

- State ownership map names actual read/write locations.
- Each recommended move eliminates a demonstrated defect or complexity, not just style preference.
- Runtime-dependent claims are verified or explicitly pending with exact checks.

## Expected output format

```markdown
## State diagnosis: <feature>

**State inventory**
| value | current owner(s) | source of truth | reads/writes |

**Findings**
- <severity> — <state ownership/cache/race issue> — evidence: <location/trace> — fix: <minimal change>

**Update flow checked**: <mutation/navigation/filter path>
**Verification**: <tests/browser/network checks run or exact pending checks>
**Handoff**: <changes, risks, follow-ups>
```

## Failure modes to avoid

- **Store-as-default**: moving state global because local code is messy, without proving multiple independent consumers need it.
- **Mirrored derived state**: storing values that can be derived from props, URL, or server cache, then debugging drift forever.
- **Cache-key hand waving**: saying "invalidate the query" without naming the exact cache key and mutation path.

More in `references/anti-patterns.md`.
