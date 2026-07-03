# Sample Task

## User prompt

"Should we add caching to speed up this endpoint? We are worried about stale data."

## Abbreviated run

- Ask for the endpoint's read/write ratio, request volume, and acceptable staleness window.
- Note that if writes are frequent relative to reads, caching may add consistency risk for little gain.
- Obstacle: the actual read/write ratio and acceptable staleness window are not yet provided.

## Evidence requests

- Approximate read/write ratio and request volume for this endpoint's data.
- The acceptable staleness window from a business/user perspective.
- Whether other consumers of this data already assume strong consistency.

## Expected output

A caching strategy review with the fit assessment marked pending until read/write ratio and staleness tolerance are confirmed, an explicit discussion of stampede risk and fallback behavior if caching proceeds, and an invalidation plan proposal (event-based invalidation vs TTL) contingent on the confirmed freshness requirement.

## Why this passes the quality bar

The agent does not treat caching as a default win, requires actual read/write and freshness evidence before recommending it, and proactively addresses stampede and fallback risk rather than treating the cache as failure-free.
