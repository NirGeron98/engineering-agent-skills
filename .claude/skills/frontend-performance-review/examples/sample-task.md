# Sample task — Frontend Performance Review

## Prompt

"The analytics route takes forever to load and Network shows repeated API calls. Find the frontend performance issue."

## Abbreviated run

1. Identified target: analytics route initial interactive load on mid-tier laptop.
2. Could not run browser locally; requested production build plus Network waterfall and bundle analyzer commands.
3. Read route entry, query hooks, chart imports, and cache config.
4. Found four widgets requesting the same summary endpoint with different unstable object keys.
5. Loaded `references/checklist.md` before handoff.

## Expected output

```markdown
## Frontend performance review: analytics route

**Target**: initial route load; repeated summary calls suspected.
**Measurements**: pending Network capture; code evidence shows duplicate query keys.

**Findings**
- High — summary endpoint is called once per widget because query keys include non-stable filter objects — fix: normalize filter key and share one cached query.
- Medium — chart library is imported by the route shell before any chart is visible — verify bundle impact, then split chart panel chunk if analyzer confirms.

**Verification**: pending: Network waterfall should show one summary request per filter set; analyzer should show chart chunk moved off shell if implemented.
```
