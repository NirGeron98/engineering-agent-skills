# Component Design Review — Anti-patterns

## Design-system cosplay
**Symptom**: a one-feature component becomes a generic platform primitive with dozens of variants.
**Instead**: support current consumers cleanly; add extension points only when a real caller needs them.

## Prop soup
**Symptom**: many booleans and partially overlapping props encode states that should be named variants or composed subparts.
**Instead**: model state intentionally and make invalid combinations impossible where the framework/type system allows.

## Caller reaches through the wall
**Symptom**: consumers use private class names, DOM shape, or refs to customize normal behavior.
**Instead**: provide an explicit slot/child/prop/event styling hook for the supported customization.

## State ownership blur
**Symptom**: caller and component both try to own open/selected/value state.
**Instead**: choose controlled, uncontrolled, or hybrid with explicit precedence and event semantics.

## Accessibility as an implementation detail
**Symptom**: labels, focus targets, and keyboard behavior cannot be controlled or tested by callers.
**Instead**: expose the hooks needed for accessible use and verify them with DOM or interaction evidence.

## Snapshot-only confidence
**Symptom**: a component is declared safe because a snapshot changed as expected.
**Instead**: verify behavior through focused interaction tests, stories, or browser checks.
