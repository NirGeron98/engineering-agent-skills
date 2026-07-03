# Accessibility Review — Anti-patterns

## Div-button drift
**Symptom**: clickable `div`/`span` elements need hand-rolled keyboard behavior and still miss semantics.
**Instead**: use native `button`, `a`, `input`, `select`, and `dialog` patterns where possible.

## ARIA camouflage
**Symptom**: ARIA attributes make inaccessible behavior appear intentional without implementing keyboard/focus requirements.
**Instead**: use ARIA only when the interaction pattern requires it and verify required behavior.

## Focus teleport
**Symptom**: opening or closing a modal/menu leaves focus lost at the document body or behind the overlay.
**Instead**: move focus intentionally on open, trap where appropriate, and restore focus on close.

## Color-only truth
**Symptom**: errors, active states, or chart distinctions are visible only by color.
**Instead**: add text, iconography, pattern, or structural cues and verify contrast.

## Axe says yes
**Symptom**: an automated tool passes, so the UI is declared accessible.
**Instead**: treat automation as a baseline; manual keyboard and dynamic-state checks are still required.

## Screen-reader fan fiction
**Symptom**: the report claims what a screen reader announces without testing DOM names or assistive tech.
**Instead**: inspect computed accessible names and use actual screen-reader evidence when announcement details matter.
