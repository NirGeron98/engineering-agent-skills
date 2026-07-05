# Form Validation Review — Anti-patterns

## Schema tunnel vision
**Symptom**: the review stops at a shared schema and never checks UI behavior or server error mapping.
**Instead**: verify the full path from field edit to server rejection and displayed error.

## Submit button silence
**Symptom**: a disabled submit button prevents progress without telling the user why.
**Instead**: show actionable validation messages and ensure keyboard users can discover them.

## Error toast evaporation
**Symptom**: server field errors appear only in a toast and disappear while the fields remain invalid.
**Instead**: map field errors to fields and preserve form-level errors until a meaningful change or retry.

## Double-submit optimism
**Symptom**: rapid clicks or Enter key sends duplicate creates.
**Instead**: gate submissions at the handler/action level, not just by button visuals.

## Async validation time travel
**Symptom**: a username check for the old value marks the new value unavailable.
**Instead**: cancel or ignore stale async validations keyed to the field value.

## Reset surprise
**Symptom**: after failed submit or partial success, the form clears user input unexpectedly.
**Instead**: reset only on confirmed success or explicit user action.
