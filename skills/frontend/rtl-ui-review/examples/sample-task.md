# Sample task — RTL UI Review

## Prompt

"Review the Hebrew dashboard. It mixes Hebrew labels, English campaign names, dollar amounts, and charts."

## Abbreviated run

1. Found locale switch sets `dir` on the app shell.
2. Loaded dashboard fixture with Hebrew labels, English campaign names, URLs, numbers, and dates.
3. Inspected screenshot and CSS for table, filters, chart legend, and row actions.
4. Found physical `margin-left`, unisolated campaign names, and over-mirrored external-link icon.
5. Loaded `references/checklist.md` before handoff.

## Expected output

```markdown
## RTL review: revenue dashboard

**Findings**
- High — campaign names with English + Hebrew punctuation render ambiguously in table cells — wrap LTR campaign tokens in `dir="ltr"`/bidi isolation.
- Medium — filter chips use `margin-left`, causing uneven spacing in RTL — use `margin-inline-start`.
- Medium — external-link icon is mirrored even though the metaphor is not direction-specific — keep it stable.

**Fixture used**: Hebrew account names, English campaign names, URLs, USD/ILS values, relative and absolute dates.
```
