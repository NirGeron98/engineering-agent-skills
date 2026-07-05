# Sample task: review a new order-confirmation email worker

## Prompt

> We just added an SQS consumer that sends order-confirmation emails. Can you review the queue setup before we roll it out? Code is in `workers/order-emails/`.

## Abbreviated agent run

1. Delivery contract: read `workers/order-emails/config.ts` and the Terraform for the queue — standard SQS (at-least-once), `visibilityTimeout: 30`, `maxReceiveCount: 5` with a DLQ `order-emails-dlq`. Recorded, cited.
2. Producer → consumer: enqueue in `checkout.service.ts` on order paid; payload is `{orderId}` only — handler re-fetches the order and customer at process time.
3. Idempotency: handler calls `mailer.send(...)` directly with no dedup — on any redelivery the customer gets a second confirmation email. Verified: no sent-marker, no idempotency key.
4. Retry/DLQ: DLQ exists; grepped alerting config — no alarm on `order-emails-dlq` depth. Failed emails will pile up unseen.
5. Crash window: `mailer.send` then `markEmailed(orderId)` then implicit ack — crash between send and mark → redelivery → duplicate email (confirms step 3).
6. Visibility timeout: send path calls an external mail API with a 25s timeout; under provider slowness the job can exceed the 30s visibility timeout and be redelivered while still running.

## Obstacle / missing evidence moment

> The agent cannot see the live DLQ depth or the CloudWatch alarm list from the repo alone.

## Exact evidence request

```
Run: aws cloudwatch describe-alarms --query "MetricAlarms[?contains(AlarmName, 'order-emails')].AlarmName"
Paste back: the alarm names (or empty list)
```

Conclusion "DLQ is unmonitored" is marked pending until this returns.

## Expected output

```markdown
## Background job & queue review: order-confirmation email worker

**Delivery contract**: at-least-once (SQS; queue.tf:12, visibilityTimeout=30, maxReceiveCount=5)
**Topology**: 1 producer (checkout.service.ts) → order-emails queue → N consumers; no ordering requirement

**Data-loss / duplication risks**
1. Non-idempotent send — trigger: redelivery on crash-before-ack or visibility-timeout
   overrun — effect: duplicate confirmation emails to customers — suggest: record a
   sent-marker keyed by orderId and check it before send (handler.ts:20)

**Failure handling gaps**
- DLQ order-emails-dlq has no depth alarm (pending: describe-alarms) — failed emails
  accumulate unseen; add an alarm + triage path
- 25s external mail timeout under a 30s visibility timeout — provider slowness causes
  self-concurrent redelivery; raise visibility timeout or shorten/heartbeat the call

**Backlog / ordering / concurrency**
- Payload is {orderId} re-fetched at process time — fine here; noted as deliberate

**Checked and clean**
- Bounded retries (maxReceiveCount=5) with DLQ routing
- Poison messages route to DLQ rather than looping

**Pending evidence**: DLQ alarm existence (awaiting describe-alarms output)
```

## Why this passes the quality bar

Delivery semantics are cited from the queue config; the duplicate-email risk names the exact non-idempotent operation and its redelivery trigger; the unmonitored-DLQ claim is marked pending rather than asserted because the agent couldn't see the alarm list; findings are ranked by customer impact.
