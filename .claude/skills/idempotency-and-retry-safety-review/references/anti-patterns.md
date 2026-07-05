# Idempotency & Retry-Safety Review — Anti-patterns

## Server-generated idempotency key
**Symptom**: the handler creates a UUID for the "idempotency key" on each invocation.
**Why it fails**: a retry is a new invocation with a new key, so it dedupes nothing; only a key stable across retries (from the client or the event id) works.
**Instead**: require the key from the client/event, and reject writes that need one but don't carry one.

## Check-then-act without a constraint
**Symptom**: `if (!exists(key)) insert(...)` in application code, no unique index.
**Why it fails**: two concurrent retries both read "not exists" and both insert — the race the check was meant to prevent.
**Instead**: back it with a unique constraint or an atomic upsert; let the database arbitrate.

## Effect-before-record crash gap
**Symptom**: call the payment API, then write the "charged" row; a crash in between leaves no record, so the retry charges again.
**Why it fails**: the side effect and its proof aren't atomic.
**Instead**: persist intent before the effect and reconcile, or use the provider's idempotency key so the external system dedupes the effect itself.

## "It's a PUT, so it's idempotent"
**Symptom**: idempotency assumed from the HTTP verb.
**Why it fails**: the verb is a convention, not an enforcement; a PUT that appends to a list or increments a counter is not idempotent.
**Instead**: judge by what the code does to state, not the method name.

## Wrong key scope
**Symptom**: dedup keyed on something too broad (per-user, so the user can only ever do the action once) or too narrow (per-request, so nothing dedupes).
**Instead**: scope the key to the unit of work that must happen exactly once (this order, this invoice, this event).

## Idempotent DB, non-idempotent side effect
**Symptom**: the row upsert is safe, but the same path also sends an email / calls a third party every time.
**Why it fails**: the review declares the operation safe because the DB write is safe, missing the un-deduped external effect.
**Instead**: assess every side effect of the operation, not just the primary write.

## Partial multi-step retry
**Symptom**: debit account A then credit account B; a retry after a mid-sequence failure re-debits.
**Instead**: make the sequence resumable/atomic (transaction, outbox, saga with idempotent steps), not just each step in isolation.

## Assuming duplicates aren't already happening
**Symptom**: declaring a path safe without checking whether duplicates already exist.
**Instead**: run a group-by on the natural key to confirm; existing duplicates are proof and prioritize the fix.
