# Sample Task

## User prompt

"The checkout E2E test fails in CI on the payment step, but passes locally."

## Abbreviated run

- Inspect CI failure logs, Playwright trace, screenshot, video, network requests, and console errors.
- Compare local command, browser, environment variables, seeded data, auth/session, and API responses.
- Obstacle: trace and HAR were not attached to the failure.

## Evidence requests

- `npx playwright test checkout --trace on --headed`
- CI trace/video/screenshot artifact URL.
- Network HAR or Playwright trace showing payment API response.

## Expected output

An E2E diagnosis with evidence table, root-cause classification, ruled-out causes, minimal fix or stabilization plan, and verification command.

## Why this passes the quality bar

The agent refuses to infer browser/network state from test code and marks dependent conclusions pending until artifacts exist.

