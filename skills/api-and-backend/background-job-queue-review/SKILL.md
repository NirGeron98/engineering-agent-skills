---
name: background-job-queue-review
description: Review the reliability of a background job or message-queue workflow — delivery semantics, retries, dead-lettering, idempotent consumers, ordering, visibility timeouts, poison messages, and backlog behavior — by reading the producer, consumer, and queue config in the actual codebase. Use when a queue/worker system is added or changed, when jobs are lost, duplicated, stuck, or reprocessed, or when someone asks "is this queue set up safely". Produces a reliability findings report per job/consumer grounded in the code and queue configuration. For diagnosing a live incident in a deployed worker, use service-debugging instead.
---

# Background Job & Queue Review

## Goal

Judge whether an async job or queue workflow will process every message correctly under the failure conditions that always eventually happen — worker crash mid-job, redelivery, duplicate, out-of-order arrival, poison message, backlog spike — and produce findings a developer can apply before those conditions hit production.

## When to use

- A new queue/worker, cron-driven job, or event consumer is proposed or implemented.
- Retry, dead-letter, concurrency, or visibility-timeout config changes.
- Jobs are reported lost, duplicated, stuck, reprocessed, or silently dropped.
- Someone asks whether a broker (SQS/RabbitMQ/Kafka/Pub-Sub/Sidekiq/Celery/etc.) setup is "safe".

## When not to use

- A live worker incident in a deployed environment needing timeline/telemetry diagnosis — use `service-debugging`.
- The concern is only whether specific write operations are safe to retry/duplicate across the service — use `idempotency-and-retry-safety-review` (this skill hands off to it for the consumer-idempotency depth).
- Inbound HTTP overload protection and shedding — use `rate-limiting-and-backpressure-review`.
- Warehouse/analytics pipeline jobs (freshness, cost, backfill) — use the `data` domain skills.

## Workflow

**Evidence access rule (applies to every step):** when you cannot read broker config, a dashboard, or queue depth yourself, output the exact command, console path, or config file for the user to provide and paste back. Mark any conclusion that depends on missing evidence as *pending*. Never assume a broker's delivery semantics — confirm them from config or docs for that specific broker/version.

1. **Establish the delivery contract from config, not assumption.** Read the broker and library config: at-least-once vs at-most-once vs claimed exactly-once, ack/nack mechanics, visibility timeout / lease duration, max receive count, prefetch/concurrency. Different brokers default differently; cite the config that sets each.
2. **Map producer → queue → consumer.** For the job under review, open the enqueue site(s), the queue definition, and the handler. Note payload shape, whether the payload carries everything the consumer needs (vs. re-fetching mutable state), and how many producers/consumers exist.
3. **Check consumer idempotency against redelivery.** At-least-once + a non-idempotent handler = duplicate side effects on every retry. Verify the handler dedupes (dedup key, natural idempotency, conditional write) or explain why redelivery is safe. For depth on a money-moving or externally-visible handler, hand off to `idempotency-and-retry-safety-review`.
4. **Check the retry and dead-letter path.** Bounded retries with backoff (not tight infinite loops); a dead-letter queue or failure table exists and is *monitored*; the distinction between retriable (transient) and non-retriable (poison) failures is made in code, not by retrying a malformed message 10 times.
5. **Check the crash window.** What happens if the worker dies after the side effect but before ack? After ack but before commit? Long jobs vs. visibility timeout — a job that outruns its visibility timeout gets redelivered and runs concurrently with itself. Find the ack-ordering and timeout mismatch.
6. **Check ordering and concurrency assumptions.** If the code assumes ordered processing, verify the broker guarantees it (most don't across partitions/consumers). Check for lost updates when N workers process related messages concurrently.
7. **Check backlog and poison behavior.** Backpressure when consumers fall behind (does the producer block, drop, or grow unboundedly?); one poison message shouldn't wedge the whole queue; oversized payloads and their limits.
8. **Report** in the output format, citing config/code for each finding, ranked by blast radius. Before reporting, load `references/checklist.md` and confirm every area was covered; consult `references/anti-patterns.md` when weighing a finding.

## Questions to ask (only when necessary)

- The broker and library versions, if not discoverable from manifests/config.
- Whether a job is expected to be strictly ordered or exactly-once, when the code is ambiguous and the broker can't guarantee it.
- Where the dead-letter queue is monitored, if no alerting config is visible.

Delivery semantics, retry config, and handler behavior must be read from code/config, not asked.

## Quality bar

- Delivery semantics stated with the config line that establishes them.
- Every "duplicate side effect" finding names the non-idempotent operation and the redelivery trigger.
- Retry path traced to a real dead-letter/failure sink, and whether it is monitored.
- Crash-window analysis references the actual ack ordering in the handler.
- Ordering/concurrency claims checked against the broker's real guarantees, not assumed.
- Findings ranked: data-loss/duplication > stuck-queue/poison > backlog > polish.

## Expected output format

```markdown
## Background job & queue review: <job / consumer>

**Delivery contract**: <at-least-once / at-most-once / ...> (from <config file:line>)
**Topology**: <N producers → queue → M consumers; ordering guarantee: ...>

**Data-loss / duplication risks**
1. <finding> — trigger: <redelivery/crash/concurrency> — effect: <duplicate charge/lost job> — suggest: <direction> (<code evidence>)

**Failure handling gaps**
- <retry/DLQ/poison/visibility-timeout finding> (<config/code evidence>)

**Backlog / ordering / concurrency**
- <finding> (<evidence>)

**Checked and clean**
- <areas verified without findings>

**Pending evidence**: <config/queue-depth awaiting user, if any>
```

## Failure modes to avoid

- **Trusting "exactly-once" on the label**: most "exactly-once" is at-least-once delivery plus idempotent processing — verify the idempotency exists, don't take the marketing term.
- **Ignoring the crash-before-ack window**: the handler looks correct on the happy path but double-acts when the worker dies mid-job; walk the crash points explicitly.
- **Unmonitored dead-letter queue**: a DLQ that exists but nobody watches is where failed jobs go to be forgotten — verify alerting, not just existence.

More in `references/anti-patterns.md`.
