# RTL UI Review — Anti-patterns

## `dir` sticker
**Symptom**: adding `dir="rtl"` to a wrapper is treated as complete RTL support.
**Instead**: verify rendered layout, controls, mixed text, numbers, overlays, and responsive states.

## Physical CSS whack-a-mole
**Symptom**: one `left` bug is patched with a conditional while many `margin-left`/`right` assumptions remain.
**Instead**: prefer logical properties and direction-aware utilities at the styling boundary.

## Bidi punctuation scramble
**Symptom**: strings like `דוח Q3 (final)` or `user@example.com` display punctuation in confusing order.
**Instead**: isolate LTR tokens with appropriate markup or Unicode handling and test real mixed strings.

## Numeric alignment flip
**Symptom**: numeric table columns align like prose and become hard to scan.
**Instead**: align numbers consistently for comparison while keeping surrounding layout RTL-aware.

## Everything mirrors
**Symptom**: logos, play buttons, clocks, charts, or brand marks are flipped mechanically.
**Instead**: mirror only direction-dependent icons and layouts; keep semantic or brand imagery stable.

## Screenshot-free confidence
**Symptom**: a review signs off RTL after reading CSS but never rendering Hebrew/Arabic/Persian/Urdu content.
**Instead**: require screenshot, browser, DOM, or visual regression evidence.
