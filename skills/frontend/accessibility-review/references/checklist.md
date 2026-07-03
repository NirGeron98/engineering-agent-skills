# Accessibility Review — Checklist

## Evidence
- [ ] Scope and user path stated
- [ ] Source files opened
- [ ] Rendered DOM, browser, screenshot, or exact pending evidence request included
- [ ] Automated checks run if available, or exact command provided

## Semantics
- [ ] Native element choice checked
- [ ] Accessible names and descriptions checked
- [ ] Labels associated with inputs and controls
- [ ] Headings and landmarks coherent
- [ ] Tables/lists use proper structure where applicable
- [ ] ARIA roles/states/properties valid and necessary

## Keyboard & focus
- [ ] Tab order follows visual/logical order
- [ ] All actions keyboard-operable
- [ ] Focus visible
- [ ] Dialog/menu/dropdown focus management checked
- [ ] Escape/close behavior and focus return checked
- [ ] Disabled controls do not trap users

## Dynamic behavior
- [ ] Errors and validation messages announced
- [ ] Loading and success states communicated when needed
- [ ] Route/page changes handled where applicable
- [ ] Live regions not noisy or stale

## Visual accessibility
- [ ] Text and UI contrast checked
- [ ] 200% zoom/reflow or responsive equivalent considered
- [ ] Reduced motion respected for animation
- [ ] Non-color cues exist for status/errors
- [ ] Hit targets and text truncation checked

## Handoff
- [ ] Findings prioritized by user impact
- [ ] Fixes are concrete and framework/project-compatible
- [ ] Pending checks are explicit
