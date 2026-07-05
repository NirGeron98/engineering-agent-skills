# Idempotency & Retry-Safety Review — Checklist

## Inventory
- [ ] Every mutating operation in scope listed (HTTP writes, consumers, webhooks, cron)
- [ ] External side effect(s) per operation named (DB, payment, email/SMS, provisioning)

## Retry sources
- [ ] Retry source(s) identified per operation (client, gateway/LB, queue, webhook, cron, manual replay)

## Classification
- [ ] Each operation classified natural-idempotent vs accumulating
- [ ] Accumulating operations flagged for mechanism check

## Mechanism verification
- [ ] Dedup mechanism located in code for each unsafe operation
- [ ] Mechanism enforced atomically (unique constraint / atomic upsert), not check-then-act
- [ ] Idempotency key is client/event-derived, not server-generated per attempt
- [ ] Key scope/uniqueness correct (per-user / per-order / per-event as appropriate)
- [ ] Key stored long enough to outlive the retry window

## Crash & multi-step safety
- [ ] Crash-between-side-effect-and-record window addressed for money-moving ops
- [ ] Multi-step/ordering-sensitive writes safe under partial retry (outbox/saga/txn boundary)

## Evidence
- [ ] Duplicate-detection query run or requested where duplicates would be observable
- [ ] Query and result recorded

## Reporting
- [ ] Operation matrix complete: every op has a verdict
- [ ] Gaps ranked by blast radius (irreversible/money first)
- [ ] Accepted-unsafe items have an explicit reason
- [ ] Pending evidence listed where access was missing
