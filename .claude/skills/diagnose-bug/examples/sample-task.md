# Sample task: intermittent duplicate-invoice bug

## Prompt

> Support says some customers occasionally get two invoices for the same order. No errors in Sentry. Started roughly two weeks ago.

## Abbreviated agent run

1. Symptom captured: duplicate invoices, same order_id, intermittent, no exceptions, onset ~2 weeks.
2. Evidence pull: query found 14 duplicate pairs; all pairs created 30–35s apart. Suspicious regularity.
3. Data path traced: `order.paid` event → SQS → `InvoiceWorker.handle()` → `createInvoice()`. Read the worker: no idempotency key; visibility timeout 30s.
4. Git log for the 2-week window: deploy added a PDF-rendering step to `handle()`, pushing slow invoices past 30s.
5. Hypotheses: (a) SQS redelivery after visibility timeout — predicted by the 30–35s gap; (b) double event publish — rejected: outbox table shows single publish per order; (c) frontend double-submit — rejected: duplicates exist for API-only orders.
6. Confirmed (a) by reproduction: test harness with a 31s-slow handler produced a duplicate.
7. Regression test written: concurrent/redelivered handling of the same event must create exactly one invoice — failed before fix.
8. Minimal fix: idempotency via unique index on `invoices.order_id` + `ON CONFLICT DO NOTHING` path in `createInvoice()`; visibility timeout raised as ops follow-up, not the fix.

## Expected output

```markdown
## Bug diagnosis: duplicate invoices for the same order

**Root cause**: PDF step added on 2026-06-18 made InvoiceWorker.handle() exceed
the 30s SQS visibility timeout → message redelivered → createInvoice() has no
idempotency guard → second invoice (src/workers/invoice.worker.ts:41)
**Evidence**: all 14 duplicate pairs 30–35s apart; reproduced with 31s-slow
handler in test harness; onset matches PDF deploy commit 9f3ac21
**Hypotheses rejected**:
- double publish — outbox shows one event per order
- client double-submit — duplicates exist on API-only orders

**Fix**: unique index on invoices.order_id + conflict-safe insert
(migrations/0142, src/services/invoice.service.ts)
**Regression test**: invoice.worker.spec.ts "redelivered event creates one
invoice" — failed before fix: yes
**Verification**: worker suite green; duplicate query returns 0 on replayed events

**Same pattern elsewhere**: ReceiptWorker also lacks idempotency (same shape) —
reported, not changed
**Follow-ups (not done)**: raise queue visibility timeout to 120s (ops config)
```

## Why this passes the quality bar

The 30–35s gap was treated as evidence, not coincidence; the cause was confirmed by a reproduction that predicted the behavior; the fix is idempotency (cause) rather than only a longer timeout (symptom); a sibling worker with the same flaw is reported instead of silently patched.
