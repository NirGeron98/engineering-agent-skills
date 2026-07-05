# Webhook Reliability Review — Checklist

## Scope
- [ ] Owned side(s) identified: inbound / outbound / both
- [ ] Provider and its documented signing + retry scheme recorded

## Inbound: security
- [ ] Signature verified before payload is trusted or triggers side effects (file:line)
- [ ] Exact scheme confirmed (HMAC over raw body / timestamped sig / mTLS)
- [ ] Signature computed over raw bytes, not a re-serialized body
- [ ] Replay protection: timestamp window enforced
- [ ] Replay protection: event-id / nonce dedup enforced

## Inbound: processing
- [ ] Receiver dedupes by provider event id before applying effects
- [ ] Handler acks quickly (2xx) and defers heavy work, avoiding provider-side timeout redelivery
- [ ] Handler crash window analyzed (effect vs. ack ordering)
- [ ] Money-moving/externally-visible handlers handed off to idempotency-and-retry-safety-review if depth needed

## Outbound: delivery
- [ ] Retries with backoff on non-2xx/timeout, bounded attempt budget
- [ ] Failed-delivery record / dead-letter exists and is observable
- [ ] Redrive/replay tool for missed deliveries exists or gap flagged
- [ ] Outbound payloads signed so receivers can verify
- [ ] Timeout on outbound call; one slow receiver can't stall the sender

## Outbound: security
- [ ] Secrets not leaked in URLs or logs
- [ ] SSRF: user/tenant-supplied destination URLs validated/allowlisted

## Ordering & visibility (both sides)
- [ ] Order assumptions checked against what the sender guarantees; timestamps/versioning used to resolve
- [ ] Delivery/receipt failures surfaced via log or alert, not silently dropped

## Reporting
- [ ] Findings ranked by blast radius (forgery/replay/data-loss first)
- [ ] Every finding cites code/config evidence
- [ ] Pending evidence listed where access was missing
