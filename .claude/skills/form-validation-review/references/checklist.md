# Form Validation Review — Checklist

## Evidence
- [ ] Form component/route opened
- [ ] Validation schema or rule code inspected
- [ ] Server endpoint/action contract or error shape inspected
- [ ] Existing tests/stories opened when present
- [ ] Runtime/browser/network evidence captured or exact pending request supplied

## Field rules
- [ ] Required/optional/default behavior listed per field
- [ ] Type, length, range, format, and cross-field rules checked
- [ ] Client and server rules compared
- [ ] Async validation rules and debounce/cancellation behavior checked
- [ ] Hidden/disabled fields and edit-vs-create differences checked

## Submission UX
- [ ] Dirty/touched behavior checked
- [ ] Loading state and double-submit prevention checked
- [ ] Success path and reset/cancel behavior checked
- [ ] Server rejection preserves user input
- [ ] Retry behavior clear after failure

## Errors & accessibility
- [ ] Field errors rendered near fields
- [ ] Form-level errors rendered for non-field failures
- [ ] Server errors mapped without dropping unknown fields
- [ ] Labels, descriptions, error associations, and focus after submit checked
- [ ] Error announcements do not rely on color alone

## Handoff
- [ ] Validation matrix includes invalid, async, server-error, success, and reset paths
- [ ] Pending business-rule or runtime evidence clearly marked
