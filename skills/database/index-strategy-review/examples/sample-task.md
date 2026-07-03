# Sample Task

## User prompt

"Review whether these proposed indexes match our most important query patterns."

## Abbreviated run

- Ask for the actual top query patterns and the proposed index list.
- Compare column order in a proposed composite index against the filter/sort order of the given queries.
- Obstacle: cardinality/selectivity data for the candidate columns is not provided.

## Evidence requests

- Approximate cardinality or distinct-value counts for the candidate index columns.
- Confirmation of current write volume on the affected table(s).

## Expected output

An index strategy review recommending indexes justified by the given query patterns, rejecting at least one proposed index for low expected selectivity or redundancy with an existing index, noting write/storage cost, and a verification plan to confirm the planner uses the new index — with cardinality-dependent conclusions marked pending until distribution data is supplied.

## Why this passes the quality bar

The agent ties every recommendation to a real query pattern, does not assume cardinality, and explicitly states write-cost tradeoffs instead of treating indexes as a free read-side win.
