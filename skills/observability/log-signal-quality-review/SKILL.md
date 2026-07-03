---
name: log-signal-quality-review
description: Review logs that are too noisy, too sparse, inconsistent, unstructured, missing correlation IDs, or not useful for debugging and operations. Use when a logging quality review, missing/over-noisy log points, correlation/context gaps, privacy/sensitive-data logging risks, recommended log changes, and verification examples are needed from actual log samples and logging code.
---

# Log Signal Quality Review

## Goal

Turn actual log samples and logging code into an evidence-based review of log usefulness, correlation, and safety.

## When to use

- Logs are too noisy, too sparse, inconsistent, unstructured, or missing correlation context.
- The user needs to improve log usefulness for debugging or operations.
- Logging may include sensitive data that shouldn't be logged.

## When not to use

- The task is diagnosing a specific service failure, not general log quality; use `skills/api-and-backend/service-debugging`.
- The concern is secrets/credentials found in logs as an exposure event; use `skills/security-review/secrets-exposure-review`.
- The task is alert/metric quality, not log content; use `skills/observability/metrics-and-alerts-review`.

## Composition and handoff rules

- Defer service debugging to `skills/api-and-backend/service-debugging`.
- Defer security secret exposure in logs to `skills/security-review/secrets-exposure-review`.
- Defer metrics/alerts to `skills/observability/metrics-and-alerts-review`.

## Evidence access rule

Inspect actual log samples/output and the logging code that produces them. If log samples, logging code, or log-level configuration are unavailable, request exact commands/log exports and mark noise/gap conclusions pending.

## Workflow

1. Sample actual logs from the relevant service/path across normal and failure conditions.
2. Identify noise (repetitive, low-value log lines) and sparsity (missing log points at key decision/error boundaries).
3. Check for correlation/context gaps: request IDs, trace IDs, user/tenant context, and consistent structured fields.
4. Check for sensitive-data logging risk (credentials, PII, tokens) without treating a finding here as a confirmed exposure incident.
5. Recommend concrete log changes (add/remove specific log points, structure fields, redact sensitive fields) with before/after examples.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to recall log content from memory instead of inspecting samples, or to add logging everywhere.

## Questions to ask (only when necessary)

- Is there an existing structured-logging convention or schema this should align with?

## Quality bar

- Findings are based on actual log samples/code, not description from memory.
- Recommendations are specific log points, not "add more logging."
- Sensitive-data findings hand off to secrets-exposure-review rather than being remediated here as an incident.

## Expected output format

```markdown
## Log signal quality review: <scope>

**Logging quality review**: <summary>
**Missing/over-noisy log points**: <specifics>
**Correlation/context gaps**: <details>
**Privacy/sensitive-data logging risks**: <details, handoff note if exposure-level>
**Recommended log changes**: <specific edits>
**Verification examples**: <before/after samples>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Logs from memory**: describing log quality without inspecting actual samples.
- **Log-everything advice**: recommending broad additional logging without naming specific points.
- **Sensitive-data conflation**: treating a found sensitive-logging pattern as a full incident instead of noting it and handing off if warranted.

More in `references/anti-patterns.md`.
