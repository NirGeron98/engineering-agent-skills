# Sample Task

## User prompt

"Our app has intermittent DB timeouts and deadlocks under load."

## Abbreviated run

- Ask for error messages, timing pattern, and current pool configuration.
- Ask for database-side lock/deadlock logs during the incident window.
- Obstacle: deadlock logs and transaction boundary code are not yet available.

## Evidence requests

- Database deadlock logs or lock/wait metrics for the incident window.
- Application connection pool configuration (size, timeout, max lifetime).
- The transaction boundaries in code for the operations involved in the timeout reports.

## Expected output

A connection/locking diagnosis with an evidence table noting error timing and load correlation, a root cause marked pending until deadlock logs and transaction code are supplied, safe mitigation candidates (e.g., reduce transaction scope, add lock-ordering consistency) framed as hypotheses to verify, and a verification loop under representative load.

## Why this passes the quality bar

The agent does not recommend raising the pool size or timeout as a first response, and it requires database-side lock evidence before asserting a root cause.
