# Sample Task

## User prompt

"Our packages have circular imports and shared utilities are growing. Map the coupling risk and propose a safer direction."

## Abbreviated run

1. Reads package manifests and build config.
2. Runs or requests dependency graph output and searches imports into `shared`.
3. Maps cycles between `orders`, `billing`, and `notifications`.
4. Finds shared date/money utilities are safe, but shared domain predicates couple billing and order state transitions.

## Obstacle moment

The dependency graph command is unknown.

Exact evidence requests:

```text
Please run the repo's dependency graph command if one exists, or paste package import output from:
rg -n "from ['\\\"](@app/shared|../shared|.*shared)" <repo-root>
Please also paste package manifest files for the affected packages.
```

## Expected output

A coupling map, high-risk dependencies, concrete examples, proposed decoupling strategy, and verification plan.

## Why this passes the quality bar

The review uses real edges, distinguishes harmless sharing from domain coupling, and avoids generic "add interfaces" advice.
