# Index Strategy Review Anti-Patterns

## Index everything

- **Symptom**: An index is proposed for every column that appears in a WHERE, JOIN, or ORDER BY clause.
- **Why it matters**: Excess indexes slow writes, increase storage, and can go unused or conflict with each other.
- **Correction**: Justify each index against actual, important query patterns and their frequency/cost.

## Cardinality guessing

- **Symptom**: A column is assumed to be selective (e.g., "status" or "type") without checking actual value distribution.
- **Why it matters**: Low-cardinality indexes often provide little benefit and add write cost for nothing.
- **Correction**: Check actual cardinality/selectivity before recommending an index on a column.

## Composite order by convenience

- **Symptom**: Composite index column order is chosen alphabetically or by declaration order instead of by query filter/sort pattern.
- **Why it matters**: Wrong column order makes the index unusable for the queries it was meant to serve.
- **Correction**: Order composite index columns to match actual equality-then-range/sort usage.

## Write-cost blindness

- **Symptom**: New indexes are recommended purely for read benefit with no mention of write/storage impact.
- **Why it matters**: Every index slows every write to that table and consumes storage.
- **Correction**: State the write/storage tradeoff for each recommended index.

## Local query timing as truth

- **Symptom**: An index's benefit is judged from a local, low-volume test rather than production-scale cardinality and load.
- **Why it matters**: Index behavior changes significantly with data volume and concurrency.
- **Correction**: Validate against production-representative data volume where possible, and mark it pending otherwise.
