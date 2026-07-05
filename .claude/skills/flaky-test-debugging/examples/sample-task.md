# Sample Task

## User prompt

"`test_cache_expiry` fails once or twice a day in CI. Please stabilize it."

## Abbreviated run

- Inspect CI history, failure logs, retry behavior, test command, random seeds, worker count, and cache fixture.
- Hypothesize time boundary, shared cache state, and order dependency.
- Obstacle: CI logs do not include seed or previous test order.

## Evidence requests

- CI failing and passing job URLs for the same test.
- `pytest tests/test_cache.py::test_cache_expiry -q --count=50`
- Command that records seed/order, or CI shard log with previous tests.

## Expected output

A flaky test diagnosis with failure pattern, hypotheses, rejected causes, stabilization plan, and repeated-run verification.

## Why this passes the quality bar

The agent does not treat a retry or one green run as repair and asks for repeated evidence.

