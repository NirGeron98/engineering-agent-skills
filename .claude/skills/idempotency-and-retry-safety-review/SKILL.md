---
name: idempotency-and-retry-safety-review
description: Audit which write operations in a backend service are safe to retry or receive twice — client retries, at-least-once queue delivery, webhook redelivery, gateway timeouts, double-clicks — and where duplicate-suppression must live, by tracing each mutating path in the actual code. Use when adding or reviewing money-moving or externally-visible writes, when duplicates are reported (double charges, double emails, duplicate rows), or when someone asks "is this safe to retry". Produces a per-operation retry-safety matrix with the exact idempotency mechanism (or gap) for each. For the API contract shape of an idempotency key, use api-design-review.
---

# Idempotency & Retry-Safety Review

## Goal

For every mutating operation in scope, establish what happens when it runs twice — and confirm each one is either naturally idempotent, protected by a deliberate dedup mechanism, or explicitly accepted as unsafe with a reason. Retries and duplicate delivery are certainties, not edge cases; this review makes the duplicate outcome a decision, not an accident.

## When to use

- Adding or reviewing writes that move money, send messages, provision resources, or are externally visible.
- Duplicates reported: double charges, duplicate emails, repeated rows, doubled inventory decrements.
- A service sits behind retrying clients, at-least-once queues, webhooks, or a gateway with timeouts.
- Someone asks whether an endpoint/handler is "safe to retry".

## When not to use

- The reliability of the queue/worker delivery mechanism itself (visibility timeout, DLQ, ordering) — use `background-job-queue-review` (it hands the consumer-idempotency depth here).
- Webhook signing/replay/delivery specifics — use `webhook-reliability-review` (hands receiver dedup depth here).
- Designing the request/response shape of an idempotency key at contract time — use `api-design-review`.
- A live duplication incident needing telemetry/timeline — use `service-debugging`; use this for the follow-up hardening.

## Workflow

**Evidence access rule (applies to every step):** when you cannot read a code path, DB schema, or run a query to check for duplicate rows yourself, output the exact file, migration, or query for the user to provide. Mark conclusions depending on missing evidence as *pending*. Never assume a write is idempotent — trace it.

1. **Enumerate the mutating operations in scope.** List every write path: HTTP handlers that mutate, queue consumers, webhook receivers, cron jobs, and the external side effects they trigger (DB writes, payment API calls, emails/SMS, third-party provisioning). This inventory is the spine of the output.
2. **Identify each operation's retry sources.** For each, name who can cause a second execution: client auto-retry, user double-submit, gateway/LB timeout-then-retry, at-least-once queue redelivery, webhook redelivery, manual replay. Different sources need different keys.
3. **Classify natural idempotency.** Is the operation a pure upsert / conditional write / set-to-value (idempotent by construction), or does it *accumulate* (INSERT, balance increment, counter, append, send)? Accumulating operations are the dangerous ones.
4. **Locate the dedup mechanism per unsafe operation.** For each accumulating op, find the protection in code: an idempotency key stored and checked, a unique constraint, a conditional update guarded by state, a natural business key. Confirm it is checked *and* enforced atomically — a check-then-act with a race is not protection.
5. **Check the key's scope and lifetime.** The idempotency key must be derived from the client/event, not generated server-side per attempt; it must cover the right uniqueness (per-user, per-order); stored long enough to outlive the retry window; and survive the crash-between-side-effect-and-record window (persist intent before the effect, or make the effect itself carry the key — e.g. Stripe idempotency keys).
6. **Check ordering-sensitive and multi-step writes.** Non-idempotent sequences (debit then credit) that partially apply on retry; whether the operation is wrapped so a retry re-runs the whole thing safely or resumes correctly (outbox/saga, transactional boundaries).
7. **Confirm with evidence where duplicates would show.** Where feasible, query for existing duplicates (group-by on the natural key) — proof that a "safe" path already isn't, or confirmation it's clean. Record the query and result.
8. **Report** the per-operation matrix, ranked by blast radius (money/irreversible first). Before reporting, load `references/checklist.md`; consult `references/anti-patterns.md` when judging whether a mechanism truly protects.

## Questions to ask (only when necessary)

- Whether a duplicate outcome is genuinely acceptable for a specific low-value operation (so it can be marked accepted, not fixed).
- The real retry behavior of an upstream client/gateway you can't see, if it changes the risk.

Whether a write is idempotent is traced from code, never assumed.

## Quality bar

- Every mutating operation in scope appears in the matrix with a verdict: idempotent / protected / unsafe-accepted / unsafe-gap.
- Each "protected" verdict cites the mechanism and confirms it's enforced atomically (no check-then-act race).
- Idempotency keys verified to be client/event-derived and correctly scoped, not server-per-attempt.
- The crash-between-effect-and-record window is addressed for money-moving ops.
- At least one duplicate-detection query run or requested where duplicates would be observable.
- Ranked: irreversible/money > user-visible > internal state > cosmetic.

## Expected output format

```markdown
## Idempotency & retry-safety review: <service / scope>

**Retry sources present**: <client / gateway / queue / webhook / cron / manual replay>

**Operation matrix**
| operation | side effect | retry source(s) | natural? | mechanism | verdict |
|---|---|---|---|---|---|
| POST /charges | payment API + row | client, gateway | no (accumulates) | idempotency-key stored+unique | protected (charges.ts:22) |
| queue: grant-credit | balance += | redelivery | no | none | UNSAFE-GAP |

**Gaps (ranked by blast radius)**
1. <operation> — duplicate effect: <double charge/grant> — trigger: <source> — suggest: <mechanism> (<evidence>)

**Verified duplicate check**: <query run + result, or requested>

**Accepted-unsafe (deliberate)**: <op — why duplicate is tolerable>

**Pending evidence**: <paths/queries awaiting user, if any>
```

## Failure modes to avoid

- **Server-generated keys**: an "idempotency key" created inside the handler on each call is unique per attempt and dedupes nothing — the key must come from the client/event.
- **Check-then-act races**: `SELECT ... if not exists then INSERT` without a unique constraint or atomic upsert — two concurrent retries both pass the check.
- **The gap between effect and record**: charging the card, then crashing before writing the "charged" marker — the retry charges again; persist intent or use the provider's idempotency key so the effect itself is deduped.

More in `references/anti-patterns.md`.
