---
name: webhook-reliability-review
description: Review the reliability and security of webhook delivery (outbound) and webhook handling (inbound) — signature verification, replay protection, retries with backoff, idempotent receipt, ordering, timeouts, and failure visibility — by reading the sender, receiver, and their config in the actual codebase. Use when a webhook producer or consumer is added or changed, when webhooks are missed, duplicated, processed out of order, or fail signature checks, or when integrating a third-party webhook (Stripe/GitHub/Twilio/etc.). Produces a webhook reliability findings report grounded in the code. For a live delivery incident in production, use service-debugging.
---

# Webhook Reliability Review

## Goal

Judge whether a webhook integration delivers and processes every event correctly and securely under the failure modes webhooks always hit — receiver down, duplicate delivery, out-of-order events, forged payloads, slow handler — and produce findings a developer can apply before events go missing or a spoofed payload is trusted.

## When to use

- A new webhook sender or receiver (endpoint) is proposed or implemented.
- Integrating a third-party provider's webhooks (payments, VCS, messaging, etc.).
- Webhooks are reported missed, duplicated, out of order, or failing verification.
- Signature, retry, or delivery-log config changes.

## When not to use

- A live production incident where events are dropping right now and you need telemetry/timeline diagnosis — use `service-debugging`.
- General write-path retry/duplicate safety across the whole service — use `idempotency-and-retry-safety-review` (this skill hands off to it for deep receiver idempotency).
- Internal queue/worker reliability that isn't HTTP-webhook-shaped — use `background-job-queue-review`.
- Pure API contract shape of the webhook payload — use `api-design-review`.

## Workflow

**Evidence access rule (applies to every step):** when you cannot read the sender/receiver code, provider dashboard settings, or a delivery log yourself, output the exact file, console path, or query for the user to provide. Mark conclusions depending on missing evidence as *pending*. Never assume a provider's signing scheme or retry policy — cite it from that provider's docs or your config.

1. **Determine which side(s) you own.** Sending webhooks, receiving them, or both. The checks differ; scope to what's in the codebase.
2. **Inbound: verify authenticity first.** Is every incoming webhook signature-verified against the shared secret / provider public key *before* the payload is trusted or parsed into side effects? Confirm the exact scheme (HMAC over raw body, timestamped signature, mTLS). A handler that acts on an unverified body is the top finding.
3. **Inbound: replay and timestamp protection.** Is a captured-and-replayed request rejected (timestamp window + nonce/event-id dedup)? Signature-valid does not mean fresh.
4. **Inbound: idempotent receipt.** Providers retry; the same event id will arrive twice. Verify the receiver dedupes by provider event id (stored, checked) before applying effects. Hand off to `idempotency-and-retry-safety-review` for money-moving handlers.
5. **Inbound: fast ack, async work.** Does the handler acknowledge quickly (2xx) and do heavy work asynchronously, or does slow processing cause the provider to time out and redeliver? Check the ack-before-work ordering and the handler's own crash window.
6. **Outbound: delivery guarantees.** For webhooks you send: retries with backoff on non-2xx/timeout, a bounded attempt budget, a dead-letter/failed-delivery record, and a signature the receiver can verify. Is there a replay/redrive tool for missed deliveries?
7. **Outbound: payload and security hygiene.** Signed payloads; secrets not leaked in URLs/logs; SSRF considerations if webhook target URLs are user-supplied (validate/allowlist destinations); timeouts on the outbound call so one slow receiver can't stall the sender.
8. **Ordering and visibility (both sides).** Whether the consumer assumes event order the sender doesn't guarantee (use event timestamps/versioning to resolve); whether failed deliveries/receipts are observable (delivery log, alert) rather than silently lost.
9. **Report** in the output format, citing code/config for each finding, ranked by blast radius (forgery/data-loss first). Before reporting, load `references/checklist.md`; consult `references/anti-patterns.md` when weighing a finding.

## Questions to ask (only when necessary)

- The provider and its documented signing/retry scheme, if not evident from code.
- Whether webhook target URLs are user/tenant-supplied (SSRF exposure), if the code is ambiguous.
- Where delivery failures are surfaced, if no logging/alerting is visible.

Signing scheme, retry config, and handler behavior come from code/docs, not from asking.

## Quality bar

- Signature verification confirmed to run before any side effect, with the file:line and the exact scheme.
- Replay protection assessed (timestamp window + dedup), not just signature presence.
- Receiver idempotency traced to a stored event-id check, or its absence flagged.
- Outbound delivery has bounded retries, a failure record, and signing — each cited.
- SSRF exposure evaluated when destinations are dynamic.
- Findings ranked: forgery/replay/data-loss > duplication > ordering > visibility > polish.

## Expected output format

```markdown
## Webhook reliability review: <integration> (<inbound/outbound/both>)

**Provider / scheme**: <provider> — signing: <HMAC-SHA256 over raw body + timestamp> (from <docs/config>)

**Security / forgery risks**
1. <finding> — e.g. signature verified after JSON parse / not at all — suggest: <fix> (<file:line>)

**Delivery / duplication risks**
- <retry, dedup, replay, fast-ack finding> (<evidence>)

**Ordering / visibility**
- <finding> (<evidence>)

**Checked and clean**
- <verified areas>

**Pending evidence**: <provider settings / delivery log awaiting user, if any>
```

## Failure modes to avoid

- **Parse-then-verify**: computing the signature over a re-serialized body, or verifying after the payload already triggered work — verify over the raw bytes, before any side effect.
- **Signature = freshness**: accepting a valid-but-replayed request because the signature checks out; a captured payload replays perfectly without timestamp/nonce checks.
- **Silent delivery loss**: outbound webhooks that fail and vanish with no failed-delivery record or redrive path — missed events become invisible until a customer notices.

More in `references/anti-patterns.md`.
