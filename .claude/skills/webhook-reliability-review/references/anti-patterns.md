# Webhook Reliability Review — Anti-patterns

## Parse-then-verify
**Symptom**: the handler `JSON.parse`s the body (and maybe acts on it) before checking the signature, or verifies a signature computed over the re-serialized object.
**Why it fails**: an attacker can send a forged payload that gets processed before rejection; re-serialization changes bytes so valid signatures fail and verification gets weakened or skipped.
**Instead**: verify the signature over the raw request bytes, before parsing or any side effect.

## Signature equals freshness
**Symptom**: a valid signature is treated as proof the request is legitimate right now.
**Why it fails**: a captured request replays with a perfectly valid signature; without a timestamp window and event-id/nonce dedup, replays are accepted.
**Instead**: enforce a timestamp tolerance and dedup on event id.

## Retry-blind receiver
**Symptom**: the receiver applies effects every time an event arrives, assuming each arrives once.
**Why it fails**: providers redeliver on timeout or non-2xx; the same event id arrives repeatedly, duplicating effects.
**Instead**: store and check the provider event id before applying effects.

## Slow synchronous handler
**Symptom**: the endpoint does all the heavy work (DB writes, external calls) before returning 2xx.
**Why it fails**: it exceeds the provider's delivery timeout, so the provider marks it failed and redelivers — compounding load and duplicates.
**Instead**: validate + persist the raw event, ack fast, process asynchronously.

## Fire-and-forget sender
**Symptom**: outbound webhooks are sent once; a non-2xx or timeout is logged (or not) and forgotten.
**Why it fails**: transient receiver outages silently lose events; there's no record and no redrive.
**Instead**: bounded retries with backoff, a failed-delivery record, alerting, and a replay tool.

## SSRF via destination URL
**Symptom**: the sender POSTs to a tenant-configured URL with no validation.
**Why it fails**: an attacker points it at `169.254.169.254` or internal services to exfiltrate or pivot.
**Instead**: validate/allowlist destinations, block internal ranges, and constrain redirects.

## Order-dependent consumer
**Symptom**: the consumer assumes `created` arrives before `updated`.
**Why it fails**: webhook order isn't guaranteed; retries reorder freely.
**Instead**: use event timestamps/versions and make handlers tolerant of out-of-order arrival.

## Invisible failure
**Symptom**: failed deliveries/receipts go to a log line nobody reads.
**Instead**: surface failures on a dashboard/alert with enough context (event id, attempt count) to redrive.
