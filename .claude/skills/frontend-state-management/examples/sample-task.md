# Sample task — Frontend State Management

## Prompt

"The reports page shows old rows after changing filters and sometimes reverts after saving a row. Figure out the state problem."

## Abbreviated run

1. Opened route, filter component, table component, query hook, and mutation hook.
2. Inventoried `filters` in local state, URL params, and query key.
3. Found query key omitted `status`, while the table copied query data into local `rows`.
4. Requested browser Network evidence because the app could not be run in the current environment.
5. Loaded `references/checklist.md` before handoff.

## Expected output

```markdown
## State diagnosis: reports page

**State inventory**
| value | current owner(s) | source of truth | reads/writes |
| filters | URL + local state | URL | route, FilterBar |
| rows | query cache + table local state | query cache | useReportsQuery, ReportsTable |

**Findings**
- High — `status` filter is not part of the query key, so cache data is reused for a different view — fix: include full normalized filters in the key.
- High — table mirrors query rows into local state, causing stale rows after mutation — fix: derive rows from query data and keep only selection locally.

**Verification**: pending browser check: change `status`, save a row, confirm one request per filter set and no old response overwrite.
```
