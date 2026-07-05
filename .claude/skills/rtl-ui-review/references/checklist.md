# RTL UI Review — Checklist

## Evidence
- [ ] Locale and `dir` mechanism identified
- [ ] Rendered RTL evidence captured or exact screenshot/browser request provided
- [ ] Mixed-direction fixture used
- [ ] Empty, loading, error, and dense data states considered

## Direction & layout
- [ ] Root/component `dir` values correct
- [ ] Physical left/right CSS checked for logical-property replacement
- [ ] Flex/grid order and alignment verified
- [ ] Popovers, menus, tooltips, drawers, and sticky elements positioned correctly
- [ ] Truncation/ellipsis preserves meaningful text
- [ ] Responsive breakpoints checked where layout changes

## Mixed content
- [ ] Hebrew/Arabic/etc. text mixed with English/product terms checked
- [ ] Emails, URLs, IDs, and code-like text isolated when needed
- [ ] Numbers and punctuation appear in correct order
- [ ] Dates, times, currency, and percentages use locale-aware formatting

## Controls
- [ ] Form label/input alignment checked
- [ ] Placeholder, helper, and error text direction checked
- [ ] Cursor/text entry behavior for mixed input considered
- [ ] Keyboard navigation order matches visual/logical order

## Data and visuals
- [ ] Tables: numeric alignment, sort icons, sticky columns, row actions checked
- [ ] Charts: axes, legends, tooltips, labels, and reading order checked
- [ ] Icons classified as mirrorable, non-mirrorable, or locale-specific

## Handoff
- [ ] Findings include fixture and screenshot/DOM evidence
- [ ] Locale assumptions stated
- [ ] Pending visual checks explicit
