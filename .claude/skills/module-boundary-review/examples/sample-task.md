# Sample Task

## User prompt

"The billing module imports user, subscription, email, and reporting internals. Review whether the module boundaries are healthy."

## Abbreviated run

1. Defines the billing boundary and reads billing package exports, imports, tests, and representative callers.
2. Uses import search to list references to `user/internal`, `subscription/internal`, `email/internal`, and `reporting/internal`.
3. Separates legitimate domain inputs from leaked implementation details.
4. Finds that billing owns invoice calculation but directly triggers reporting formatters and email templates.

## Obstacle moment

The agent cannot determine team ownership or whether reporting is deployed separately.

Exact evidence requests:

```text
Please share any ownership map for billing, reporting, email, and subscription modules.
Please provide deployment boundaries for these modules if they are not all deployed together.
```

## Expected output

A boundary review that names unclear responsibilities, boundary violations, proposed changes, migration steps, and verification checks.

## Why this passes the quality bar

The review cites concrete imports and callers, avoids a broad rewrite, and hands deeper circular dependency analysis to `dependency-and-coupling-review` if needed.
