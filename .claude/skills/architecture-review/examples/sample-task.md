# Sample Task

## User prompt

"Review the proposed architecture for moving billing notifications out of the monolith into a separate service. We have a rough diagram and partial repo access."

## Abbreviated run

1. Reads the proposal and diagram.
2. Opens monolith billing notification entry points, queue/event code, current retry logic, and deployment docs that are available.
3. Maps components: monolith billing, notification worker, event transport, customer preferences, email provider, observability.
4. Finds that ownership and failure handling are documented, but idempotency and replay behavior are not evidenced.

## Obstacle moment

The agent cannot inspect production retry metrics, event volume, or actual queue configuration.

Exact evidence requests:

```text
Please provide the queue/topic configuration for billing notification events, including retry/dead-letter settings.
Please paste recent volume/error metrics for billing notification sends and retries.
Please share any existing ADR or incident postmortem related to notification delivery.
```

## Expected output

An architecture review report with strengths, risks, open questions, recommended changes, and a tradeoff summary. Idempotency, replay, and operational capacity conclusions remain pending until the requested evidence is available.

## Why this passes the quality bar

The review checks the diagram against repository evidence, avoids pretending missing runtime data was inspected, and hands detailed rollout readiness to `.claude/skills/release-and-rollback-readiness` if deployment risk becomes central.
