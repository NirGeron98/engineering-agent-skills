# Sample task: retry-safety audit before enabling gateway retries

## Prompt

> We're about to turn on automatic retries at our API gateway (retries 5xx and timeouts). Before we do, can you check which of our write endpoints are safe? The payments and notifications code is in `src/billing/` and `src/notify/`.

## Abbreviated agent run

1. Inventory: `POST /charges` (payment API + `charges` row), `POST /refunds` (refund API + row), `POST /invoices/:id/send` (email + `sent_at` update), `POST /credits/apply` (balance increment). Four mutating ops in scope.
2. Retry sources: gateway timeout-then-retry (the new one), plus existing client retries. Both re-run the same request.
3. Classify: `/charges` and `/refunds` accumulate (external money); `/invoices/send` accumulates (email + a set-to-value on sent_at); `/credits/apply` accumulates (balance +=).
4. Mechanisms:
   - `/charges` passes a client-supplied `Idempotency-Key` through to Stripe and stores it with a unique index — protected, atomic.
   - `/refunds` generates its idempotency key server-side inside the handler — dedupes nothing on retry. GAP.
   - `/invoices/send` guards on `if (!invoice.sent_at) sendEmail()` then sets `sent_at` — check-then-act with no lock; two concurrent retries both send. GAP.
   - `/credits/apply` does `balance = balance + amount` keyed on nothing. GAP (double credit).
5. Duplicate check: proposed a group-by on `credits(idempotency_ref)` — but there's no such column; instead grouped `credits` by `(user_id, source_txn_id, amount, date_trunc('minute', created_at))` to look for existing dupes.

## Obstacle / missing evidence moment

> The agent can't run queries against the production billing DB to check for existing duplicate credits.

## Exact evidence request

```
Run (read replica):
  SELECT user_id, source_txn_id, count(*)
  FROM credits GROUP BY 1,2 HAVING count(*) > 1 LIMIT 20;
Paste back: the rows (or "0 rows")
```

The "already duplicating" claim for `/credits/apply` is marked pending until this returns.

## Expected output

```markdown
## Idempotency & retry-safety review: billing + notifications

**Retry sources present**: gateway timeout-retry (new), client retry

**Operation matrix**
| operation | side effect | retry source | natural? | mechanism | verdict |
|---|---|---|---|---|---|
| POST /charges | Stripe charge + row | gateway, client | no | client Idempotency-Key → Stripe + unique idx | protected (charges.ts:22) |
| POST /refunds | Stripe refund + row | gateway, client | no | server-generated key | UNSAFE-GAP |
| POST /invoices/:id/send | email + sent_at | gateway, client | no | check-then-act, no lock | UNSAFE-GAP |
| POST /credits/apply | balance += | gateway, client | no | none | UNSAFE-GAP |

**Gaps (ranked by blast radius)**
1. /credits/apply — duplicate: double credit — trigger: gateway retry — suggest: require
   a source_txn_id, unique-constrain (user_id, source_txn_id), upsert (credits.ts:14)
2. /refunds — duplicate: double refund — trigger: retry — suggest: pass a client/event key
   to Stripe and store it, like /charges (refunds.ts:9)
3. /invoices/send — duplicate email — trigger: concurrent retries — suggest: atomic
   UPDATE ... WHERE sent_at IS NULL RETURNING, send only if a row was updated

**Verified duplicate check**: pending — group-by on credits(user_id, source_txn_id)

**Accepted-unsafe (deliberate)**: none proposed; all four need work before gateway retries

**Pending evidence**: existing-duplicate query on credits (awaiting user)
```

## Why this passes the quality bar

Every write operation appears with an explicit verdict; the "protected" verdict for /charges cites the atomic mechanism; each gap names the duplicate outcome, the retry trigger, and a concrete fix; the claim that credits may already be duplicating is marked pending rather than asserted because the query couldn't be run.
