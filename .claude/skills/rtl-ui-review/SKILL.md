---
name: rtl-ui-review
description: Review right-to-left and bidirectional frontend UI quality across layout mirroring, dir handling, logical CSS, mixed-language text, numbers, icons, tables, forms, charts, truncation, and locale formatting. Use for RTL languages such as Arabic, Hebrew, Persian, or Urdu, especially mixed LTR/RTL dashboards; requires browser/screenshot/DOM evidence and produces prioritized bidi findings.
---

# RTL UI Review

## Goal

Find and fix RTL and bidirectional UI defects that only appear when language direction, mixed text, locale formatting, and mirrored layouts are exercised in the rendered product.

## When to use

- Reviewing a UI in an RTL locale or adding RTL support.
- Mixed RTL/LTR content appears: Hebrew/English names, URLs, codes, numbers, dates, currency, or product terms.
- Tables, forms, charts, icons, truncation, or alignment look wrong in RTL.

## When not to use

- General accessibility issues not tied to directionality; use `accessibility-review`.
- General internationalization copy extraction; use a localization/i18n workflow if available.
- Component boundary review without RTL behavior; use `component-design-review`.

## Workflow

**Evidence access rule:** when you cannot run the app in an RTL locale, inspect the rendered DOM, or view screenshots, output the exact command, route, locale toggle, fixture, or screenshot request and mark visual/bidi conclusions pending. Do not infer mirroring quality from CSS source alone.

1. **Establish direction context.** Identify locale mechanism, `dir` source, document/root direction, component overrides, and whether mixed-direction content is expected. Use generic RTL rules; Hebrew examples are illustrative, not assumptions.
2. **Render representative states.** Check at least one dense, real data state and one empty/error/loading state. Include mixed RTL/LTR strings, numbers, emails/URLs/codes, dates, currency, punctuation, and long text.
3. **Inspect layout and CSS directionality.** Prefer logical properties (`inline-start`, `margin-inline`, etc.) over left/right where appropriate; verify flex/grid order, alignment, spacing, popovers, sticky columns, and truncation.
4. **Check controls and icons.** Verify form labels, input text direction, validation messages, navigation icons, chevrons, progress indicators, media controls, and icons that should or should not mirror.
5. **Check data displays.** Tables, charts, badges, status text, numeric columns, date/time/currency formatting, sort indicators, and mixed-language cell content.
6. **Verify with browser evidence.** Use screenshots, DOM, computed styles, visual regression, or manual checks. If unavailable, give exact fixture and checks for the user to run.
7. **Write the handoff.** Before writing it, load `references/checklist.md` and verify every item; consult `references/anti-patterns.md` when direction, punctuation, icon mirroring, or locale formatting is ambiguous.

## Questions to ask (only when necessary)

- Which RTL locales are supported and whether locale or content controls direction.
- Whether brand/product icons are intentionally non-mirrored.
- Access to RTL route, test fixture, screenshot, or locale toggle.

## Quality bar

- Findings cite rendered evidence or are marked pending with exact reproduction steps.
- Mixed-direction content is tested, not just full-page mirroring.
- Locale formatting and semantic icon mirroring are handled separately from layout direction.

## Expected output format

```markdown
## RTL review: <UI/path>

**Direction setup**: <locale/dir mechanism and states reviewed>
**Evidence**: <screenshots, DOM, files, pending requests>

**Findings**
- <severity> — <bidi/layout/formatting issue> — evidence: <location/check> — fix: <change>

**Fixture used**: <mixed-language data>
**Verification**: <browser/screenshot/visual checks run or pending>
**Handoff**: <must-fix, follow-ups, locale assumptions>
```

## Failure modes to avoid

- **Mirror-only review**: checking page-level `dir=rtl` but missing mixed English/Hebrew strings, numbers, and punctuation.
- **Left/right fossilization**: patching one symptom while leaving physical CSS properties that will fail in the next component.
- **Icon over-mirroring**: flipping logos, media playback, or semantic icons that should remain direction-neutral.

More in `references/anti-patterns.md`.
