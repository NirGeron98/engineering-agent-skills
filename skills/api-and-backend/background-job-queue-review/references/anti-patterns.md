# Background Job & Queue Review — Anti-patterns

## Exactly-once faith
**Symptom**: the review accepts "exactly-once" from a config flag or vendor page and skips idempotency checks.
**Why it fails**: virtually all "exactly-once" is at-least-once delivery plus idempotent processing; the flag doesn't remove duplicates from crash/redelivery windows.
**Instead**: treat delivery as at-least-once unless proven otherwise, and verify the consumer is idempotent.

## Happy-path-only reading
**Symptom**: the handler is judged on the case where it runs once, start to finish, successfully.
**Why it fails**: queues exist because things fail; the interesting behavior is on crash, redelivery, and timeout.
**Instead**: walk the crash-before-ack, crash-after-ack, and timeout-during-job cases explicitly.

## The unmonitored DLQ
**Symptom**: "there's a dead-letter queue, so failures are handled."
**Why it fails**: a DLQ nobody watches silently accumulates lost work; it's discovered during the next incident.
**Instead**: require alerting/monitoring on the DLQ, and a defined path for reprocessing or triaging its contents.

## Visibility-timeout blindness
**Symptom**: a job that can take minutes runs under a 30-second visibility timeout; nobody notices.
**Why it fails**: the broker redelivers the still-running job, so it executes concurrently with itself — duplicate side effects, race conditions.
**Instead**: compare worst-case job duration to the visibility/lease timeout; extend, heartbeat, or chunk.

## Poison-message wedge
**Symptom**: a malformed message fails, is retried forever or blocks the partition, and the whole consumer stalls.
**Instead**: cap retries, route non-retriable failures to the DLQ, and keep one bad message from halting the queue.

## Assumed ordering
**Symptom**: the consumer assumes messages arrive in the order produced.
**Why it fails**: most brokers don't guarantee global order across partitions/consumers; concurrency reorders freely.
**Instead**: verify the ordering guarantee in config; if the logic needs order, make it order-independent or use the broker's ordering key/partition.

## Fat mutable payload vs. stale re-fetch
**Symptom**: the payload carries a snapshot that's stale by processing time, or the handler re-fetches state that changed since enqueue — and neither choice is deliberate.
**Instead**: decide explicitly whether the job acts on enqueue-time or process-time state, and make the payload/fetch match that intent.

## Review-as-rebuild
**Symptom**: asked to review one consumer, the review proposes replacing the broker or redesigning the whole job system.
**Instead**: scope findings to the job under review; flag larger platform concerns separately.
