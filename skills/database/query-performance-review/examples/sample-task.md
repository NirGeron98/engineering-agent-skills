# Sample Task

## User prompt

"This query is slow in production but fast locally. Review the query plan and recommend safe fixes."

## Abbreviated run

- Ask for the query text and its production `EXPLAIN ANALYZE` output.
- Compare estimated vs actual rows; find a large misestimation on a filtered join.
- Obstacle: production plan output and current index definitions are not yet provided.

## Evidence requests

- `EXPLAIN ANALYZE` output for the query run against production (or a production-like replica).
- Current index definitions on the tables involved.
- Approximate production row counts vs local row counts for the tables involved.

## Expected output

A query performance review with an evidence table (plan nodes, estimated vs actual rows, indexes used/missing), a likely bottleneck tied to the misestimation or missing index, recommended query/index changes with write-cost risk noted, and a verification plan — with bottleneck and recommendation marked pending until the production plan and index definitions are supplied.

## Why this passes the quality bar

The agent does not conclude from local timing alone, requires the actual plan before diagnosing, and states write-cost risk for any index recommendation.
