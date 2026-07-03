# Component Design Review — Checklist

## Evidence
- [ ] Component source opened
- [ ] At least two callers, or all callers if fewer than two, inspected
- [ ] Tests, stories, or usage docs inspected when present
- [ ] Visual/interactive behavior verified by browser, Storybook, screenshot, or marked pending with exact request

## Boundary
- [ ] Component responsibility stated in one sentence
- [ ] Caller-owned responsibilities listed
- [ ] Data fetching, routing, persistence, and domain policy either justified inside or moved out
- [ ] Controlled/uncontrolled state contract explicit

## API
- [ ] Required vs optional props/inputs clear
- [ ] Events/callbacks/emits named by user intent, not implementation detail
- [ ] Loading, empty, disabled, error, and success states represented
- [ ] Defaults and escape hatches match existing project conventions
- [ ] Type definitions or runtime validation cover public contract

## Composition & styling
- [ ] Slots/children/render regions cover real customization needs
- [ ] Styling boundary defined: tokens/classes/CSS modules/theme/inline constraints
- [ ] Responsive behavior and overflow/truncation considered
- [ ] No consumer needs to target private DOM or class names

## Accessibility & tests
- [ ] Label/focus/keyboard hooks available where relevant
- [ ] ARIA usage is necessary and valid, not decorative
- [ ] Stable selectors or roles support tests without private internals
- [ ] Existing or proposed tests cover the component contract and a key interaction

## Handoff
- [ ] Must-fix findings separated from optional improvements
- [ ] Recommendations include concrete API shape or diff direction
- [ ] Pending visual/runtime evidence clearly named
