# Sample Task

## User prompt

"Review whether this internal admin API has safe access boundaries across user roles and service accounts."

## Abbreviated run

- Inspect route definitions, role/permission checks, and any service-account trust relationships used by the admin API.
- Trace whether admin endpoints verify caller role and, for resource-specific actions, ownership/tenant match.
- Obstacle: one endpoint calls a downstream internal service whose own permission enforcement code is not in the reviewed files.

## Evidence requests

- The downstream service's authorization code or documentation on what it trusts from the caller.
- Role/permission definitions for the admin API (roles table, policy file, or middleware config).

## Expected output

A boundary review with the privilege map, a finding that resource-specific admin actions check existence but not ownership (if found in code), a confused-deputy risk marked pending on the downstream service until its enforcement code is seen, and a narrowing-focused remediation plan.

## Why this passes the quality bar

The agent traces boundary claims to actual code, flags the existence-only gap if present, and does not assume the downstream service re-verifies permission without evidence.
