# Sample Task

## User prompt

"Write a runbook for the nightly import job failing."

## Abbreviated run

- Inspect job docs, scheduler config, logs query examples, dashboard links, owners, and previous incident notes.
- Draft read-only diagnosis before rerun or backfill steps.
- Obstacle: production log query access is unavailable.

## Evidence requests

- Exact alert text and job name.
- Log query or pasted recent failure logs.
- Scheduler URL or command output.
- Owner/escalation contact if not documented.

## Expected output

A runbook with trigger, impact, read-only evidence commands, mitigation/rerun guardrails, validation, escalation, and pending access notes.

## Why this passes the quality bar

The procedure does not start by rerunning the job and every unsafe action has prerequisites and validation.

