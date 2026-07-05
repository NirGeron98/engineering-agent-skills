# Sample Task

## User prompt

"Design integration tests for order creation through API, database, and queue publication."

## Abbreviated run

- Inspect order handler, DB schema, event publisher, existing integration helpers, and CI commands.
- Decide API and DB should be real; queue can be a test broker or captured publisher depending on harness evidence.
- Obstacle: cleanup strategy for queue messages is not visible.

## Evidence requests

- Existing integration test helper files.
- CI command and average runtime for integration tests.
- Queue test broker or local emulator configuration.

## Expected output

An integration test design with dependency strategy, isolated fixtures, cleanup plan, idempotency/retry cases, and verification commands.

## Why this passes the quality bar

The design tests the actual API/DB boundary and marks queue strategy pending until harness evidence is available.

