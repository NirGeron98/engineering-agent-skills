---
name: api-integration-debugging
description: Debug frontend/backend integration failures where a UI flow misbehaves despite an API response: request/response shape, auth headers/cookies, CORS, error mapping, retries, stale cache, environment variables, and loading states. Use when the UI shows empty/wrong/error data, a successful backend response is not rendered, or browser Network evidence disagrees with UI state; hands proven server-side defects to service-debugging and contract design questions to api-design-review.
---

# API Integration Debugging

## Goal

Determine whether a UI failure is caused by frontend request construction, response parsing, auth/session transport, environment/config, cache state, or backend behavior, using browser/network and code evidence before changing either side.

## When to use

- The UI shows empty, stale, or error data despite apparently successful API responses.
- Requests differ between browser, tests, curl, or environments.
- Auth headers/cookies, CORS, retries, error mapping, env vars, or client adapters are suspected.

## When not to use

- API contract shape/design review before implementation; use `api-design-review`.
- Proven backend live-service defect, latency, 5xx, or dependency issue; use `service-debugging`.
- General state ownership problem after the integration boundary is proven correct; use `frontend-state-management`.

## Workflow

**Evidence access rule:** when you cannot inspect browser Network, run the app, reproduce the flow, or call the API yourself, output exact commands/manual captures for the user and mark integration conclusions pending. Do not infer request/response behavior from client code alone.

1. **Reproduce the failing flow.** Capture route, user state, environment, request URL/method/status, request payload, response body, headers, cookies/auth, CORS preflight, and console errors. If unavailable, request a HAR/screenshot/curl equivalent.
2. **Compare expected vs actual contract.** Inspect generated types, client adapter, API docs/schema, backend handler if available, and the UI mapping. Distinguish design mismatch from implementation bug.
3. **Trace request construction.** Check base URL/env vars, path params, query params, body serialization, content type, auth header/cookie mode, credentials, locale/tenant headers, and feature flags.
4. **Trace response handling.** Check parsing, normalization, empty-state conditions, pagination envelope, null/undefined handling, date/number conversions, error mapping, and loading/success transitions.
5. **Check cache and retries.** Inspect query keys, invalidation, stale data, retry policy, dedupe, cancellation, optimistic updates, and whether old data masks a good response.
6. **Classify ownership.** If the request is wrong, fix frontend integration. If the response violates the documented contract or server errors, hand off to `service-debugging` for live behavior or `api-design-review` for contract redesign.
7. **Write the handoff.** Before writing it, load `references/checklist.md` and verify every item; consult `references/anti-patterns.md` when tempted to blame backend, CORS, or cache without evidence.

## Questions to ask (only when necessary)

- Credentials or environment access needed to reproduce the flow.
- Whether the observed API response is from local, staging, production, mock, or test fixture.
- A HAR/network capture when the browser cannot be run.

## Quality bar

- Diagnosis includes concrete request and response evidence or marks it pending.
- Ownership is classified at the integration boundary, not by guess.
- Fix or handoff names the exact file, adapter, endpoint, cache key, or environment variable involved.

## Expected output format

```markdown
## API integration diagnosis: <flow>

**Reproduction**: <route/env/user state>
**Network evidence**: <method URL status request/response summary, headers/auth/CORS notes>

**Boundary classification**: <frontend request / frontend parsing / cache / env / backend behavior / contract design>
**Findings**
- <severity> — <issue> — evidence: <network/code location> — fix/handoff: <action>

**Verification**: <browser/curl/tests run or exact pending checks>
**Handoff**: <owner, related skills, pending evidence>
```

## Failure modes to avoid

- **Backend blame by status code**: treating any bad UI after an API call as a server problem without inspecting parsing, cache, and request construction.
- **CORS cargo cult**: changing CORS settings before proving browser preflight or credential behavior is the failing step.
- **Successful-response blindness**: seeing HTTP 200 and ignoring pagination envelopes, empty arrays, null fields, or client-side filters that drop the data.

More in `references/anti-patterns.md`.
