# API Integration Debugging — Anti-patterns

## The 200 OK mirage
**Symptom**: a successful response is assumed to mean the UI should render data.
**Instead**: inspect the body shape, envelope, client mapping, filters, and empty-state conditions.

## Curl parity myth
**Symptom**: curl works, so the browser client is declared fine.
**Instead**: compare browser credentials, cookies, CORS, headers, base URL, and preflight behavior.

## Mock drift
**Symptom**: local mocks or fixtures have a shape the real API no longer returns.
**Instead**: compare generated types/schemas and real network responses, then update one source of truth.

## Environment roulette
**Symptom**: frontend calls staging API from local UI, or production CDN config points at the wrong API base.
**Instead**: record the environment, base URL, and build-time/runtime variables before debugging payloads.

## Cache cover-up
**Symptom**: a fixed response still does not appear because stale cache or optimistic state masks it.
**Instead**: inspect cache keys, invalidation, stale time, and selectors before blaming the API again.

## CORS broadening reflex
**Symptom**: `*` origins or credential changes are proposed without a captured preflight failure.
**Instead**: fix CORS only after browser evidence shows the CORS policy is the failing boundary.
