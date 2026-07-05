# Sample task: review a Stripe webhook receiver

## Prompt

> Please review our Stripe webhook handler before we go live with subscriptions. It's `routes/webhooks/stripe.ts`.

## Abbreviated agent run

1. Scope: inbound only (we receive Stripe events; Stripe sends). Provider scheme from Stripe docs: HMAC-SHA256 over the raw body with a timestamped `Stripe-Signature`, tolerance-checked.
2. Security: handler calls `express.json()` globally, so `req.body` is already parsed; `stripe.webhooks.constructEvent` needs the *raw* body. Verified: signature verification will fail or is being bypassed — read shows a try/catch that logs and continues to process on failure. Top finding: events processed without valid signatures.
3. Replay: `constructEvent` enforces the timestamp tolerance when given the raw body — but since raw body isn't available, this protection is also defeated. Pending confirmation of the raw-body middleware config.
4. Idempotency: handler upserts subscription state keyed by `event.data.object.id` but does not record `event.id`; a redelivered `invoice.paid` re-runs the credit-grant side effect. Duplicate grants possible.
5. Fast ack: handler awaits a downstream billing API call before returning 200 — under slowness Stripe times out at ~10s and redelivers.

## Obstacle / missing evidence moment

> Whether a raw-body parser is mounted specifically for the Stripe route isn't visible in the file itself; it depends on app-level middleware order.

## Exact evidence request

```
Show: the middleware registration in app.ts around express.json() and any
express.raw({type:'application/json'}) mounted for /webhooks/stripe
Paste back: those lines
```

Signature and replay findings are marked pending until the middleware order is confirmed.

## Expected output

```markdown
## Webhook reliability review: Stripe subscriptions (inbound)

**Provider / scheme**: Stripe — HMAC-SHA256 over raw body + timestamp (Stripe-Signature)

**Security / forgery risks**
1. Signature failures are caught and processing continues — unverified events applied —
   suggest: on constructEvent throw, return 400 and do nothing (stripe.ts:31) [pending
   raw-body middleware confirmation]
2. Raw body likely unavailable (global express.json) — verification can't succeed over
   parsed body — suggest: mount express.raw for /webhooks/stripe (pending app.ts)

**Delivery / duplication risks**
- No event.id dedup — redelivered invoice.paid re-grants credits — suggest: persist
  processed event.id and skip if seen (stripe.ts:40)
- Handler awaits billing API before 200 — Stripe timeout → redelivery — suggest: persist
  raw event, ack 200, process async

**Ordering / visibility**
- Consumer assumes customer.created precedes subscription events — use event created-at

**Checked and clean**
- Endpoint restricted to POST; secret loaded from env, not hardcoded

**Pending evidence**: raw-body middleware order (awaiting app.ts lines)
```

## Why this passes the quality bar

The signature finding is tied to the exact scheme and the parse-order problem, not generic advice; the duplicate-grant risk names the specific handler and missing event-id check; the two findings that depend on middleware order are explicitly marked pending rather than asserted.
