# Sample task — API Integration Debugging

## Prompt

"The customers page shows empty data, but the backend logs say `GET /customers` returns 200 with rows."

## Abbreviated run

1. Requested Network capture because the app could not be run locally.
2. Opened customers route, API client, query hook, and response mapper.
3. Compared backend response envelope `{ data: [...] }` with mapper expecting `{ customers: [...] }`.
4. Checked cache key included tenant and filters.
5. Loaded `references/checklist.md` before handoff.

## Expected output

```markdown
## API integration diagnosis: customers list

**Network evidence**: pending HAR; backend log says 200 with rows, not enough by itself.
**Boundary classification**: frontend response parsing, pending browser confirmation.

**Findings**
- High — client mapper reads `response.customers`, but the API envelope returns `data` — evidence: `customersClient.ts` vs OpenAPI schema — fix: map `response.data` or regenerate client.

**Verification**: capture browser response body, run customers route test with real envelope, confirm non-empty rows render.
```
