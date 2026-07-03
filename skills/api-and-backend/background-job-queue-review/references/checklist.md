# Background Job & Queue Review — Checklist

## Delivery contract
- [ ] Broker + library version identified from config/manifests
- [ ] Delivery semantics (at-least-once / at-most-once / exactly-once claim) cited from config
- [ ] Ack/nack mechanics and visibility timeout / lease duration recorded
- [ ] Max receive count / retry limit recorded

## Producer → consumer mapping
- [ ] Enqueue site(s) read; payload shape documented
- [ ] Payload self-sufficiency checked (carries needed data vs. re-fetches mutable state)
- [ ] Producer and consumer counts known

## Idempotency & redelivery
- [ ] Handler behavior under redelivery determined (dedupes / naturally idempotent / unsafe)
- [ ] Dedup key or conditional write located in code, or absence flagged
- [ ] Externally-visible/money-moving handlers handed off to idempotency-and-retry-safety-review if depth needed

## Failure handling
- [ ] Retries bounded with backoff; no tight infinite retry loop
- [ ] Dead-letter queue / failure sink exists
- [ ] DLQ / failure sink is monitored (alerting evidence), not just present
- [ ] Retriable vs non-retriable (poison) failures distinguished in code

## Crash window & timeouts
- [ ] Behavior on crash before ack and after ack analyzed
- [ ] Job duration vs visibility timeout checked (no self-concurrent redelivery)

## Ordering, concurrency, backlog
- [ ] Ordering assumptions checked against broker guarantees
- [ ] Concurrent processing of related messages checked for lost updates
- [ ] Backlog behavior known (block / drop / unbounded growth)
- [ ] Single poison message cannot wedge the queue
- [ ] Payload size limits considered

## Reporting
- [ ] Findings ranked by blast radius
- [ ] Every finding cites config or code evidence
- [ ] Pending evidence listed where access was missing
