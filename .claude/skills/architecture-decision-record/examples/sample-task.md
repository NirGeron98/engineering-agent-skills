# Sample Task

## User prompt

"We decided to use an event-driven approach instead of direct synchronous calls for order fulfillment. Draft an ADR with tradeoffs and revisit triggers."

## Abbreviated run

1. Searches for existing ADRs and finds `docs/adr/0004-cache-strategy.md`.
2. Reads the design issue, order service callers, and fulfillment integration notes.
3. Drafts an ADR using the existing status and heading style.
4. Captures synchronous calls, event-driven messaging, and scheduled polling as considered options.

## Obstacle moment

The agent cannot find the decision owner or expected order volume.

Exact evidence requests:

```text
Please provide the owner for the fulfillment integration decision.
Please paste the expected order volume/SLA assumptions, or confirm that they should remain pending in the ADR.
```

## Expected output

An ADR draft with status, context, decision, options considered, consequences, rejected alternatives, revisit trigger, owners, follow-up actions, and evidence links.

## Why this passes the quality bar

The ADR follows the repo convention, records tradeoffs rather than selling the decision, and labels missing ownership/scale assumptions instead of inventing them.
