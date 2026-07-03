# Sample task — Component Design Review

## Prompt

"Review our new reusable `DataTable` component before we roll it into three dashboards."

## Abbreviated run

1. Opened `DataTable.tsx`, three callers, `DataTable.stories.tsx`, and existing `ListTable`.
2. Found the repo convention: tables accept column definitions and caller-owned row fetching.
3. Noted `DataTable` fetches internally, making cache invalidation and empty/error states inconsistent with existing screens.
4. Could not run Storybook; requested `npm run storybook -- --host 127.0.0.1` screenshot for sticky header and overflow behavior, marked visual conclusions pending.
5. Loaded `references/checklist.md` before handoff.

## Expected output

```markdown
## Component review: DataTable

**Intended role**: render tabular data and table interactions; caller owns fetching and cache invalidation.
**Evidence reviewed**: DataTable.tsx, three dashboard callers, ListTable.tsx, story file; sticky-header visual check pending.

**Findings**
- High — internal fetcher makes the component incompatible with existing cache ownership — evidence: DataTable.tsx `useEffect` fetch + callers already use query hooks — recommendation: accept `rows`, `loading`, `error`, and `onRefresh`.
- Medium — column cell customization requires private class targeting — evidence: dashboard caller CSS — recommendation: add a documented cell render/slot API.

**Verification**: unit tests need row click + loading/empty/error stories; Storybook screenshot pending.
```
