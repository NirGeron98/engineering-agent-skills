# API Integration Debugging — Checklist

## Evidence
- [ ] Failing route/flow and environment recorded
- [ ] Browser Network/HAR/curl evidence captured or exact pending request supplied
- [ ] Console errors checked
- [ ] Relevant frontend client/adapter/hook/store code opened
- [ ] Contract/schema/backend handler inspected if available

## Request
- [ ] Base URL and environment variables checked
- [ ] Method, path params, query params, and body shape checked
- [ ] Serialization/content type checked
- [ ] Auth header/cookie/credentials mode checked
- [ ] Tenant/locale/feature headers checked where relevant
- [ ] CORS preflight and response headers checked when browser blocks request

## Response
- [ ] Status and response body inspected
- [ ] Pagination/envelope shape checked
- [ ] Null/empty/error cases mapped correctly
- [ ] Date/number/string conversions checked
- [ ] UI filters and selectors checked for dropped data
- [ ] Loading/success/error transitions checked

## Cache & retry
- [ ] Query/cache key includes all relevant inputs
- [ ] Invalidation/update after mutation checked
- [ ] Retry/cancellation/dedupe behavior checked
- [ ] Stale data or optimistic state cannot mask result

## Handoff
- [ ] Boundary owner classified with evidence
- [ ] Exact fix location or skill handoff named
- [ ] Pending network/runtime evidence explicitly marked
