# Sample Task

## User prompt

"Review this README. New contributors say setup does not work."

## Abbreviated run

- Inspect README, package scripts, lockfile, CI workflow, env examples, tests, and docs.
- Compare README commands to actual script names.
- Obstacle: the agent cannot run the database-dependent setup locally.

## Evidence requests

- `npm install`
- `npm test`
- `npm run dev`
- Output from database setup command named in README.

## Expected output

A README review with verified commands, stale setup findings, missing env prerequisites, suggested structure, and patch plan.

## Why this passes the quality bar

The review is grounded in repository evidence and marks setup commands pending instead of assuming they work.

