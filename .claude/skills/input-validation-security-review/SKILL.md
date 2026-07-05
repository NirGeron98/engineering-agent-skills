---
name: input-validation-security-review
description: Review untrusted input handling for injection, unsafe parsing, deserialization, path traversal, SSRF-style request forwarding, file upload risks, or validation gaps. Use when an input surface map, trust boundary findings, validation/normalization gaps, safe remediation plan, and test recommendations are needed. Defensive only, no exploit payloads.
---

# Input Validation Security Review

## Goal

Turn untrusted-input handling code into an evidence-based trust-boundary review with concrete remediation and test recommendations, without exploit payload construction.

## When to use

- Code accepts untrusted input (user request, file upload, external API response, deserialized payload) and validation/trust boundaries need review.
- Injection, path traversal, SSRF-style forwarding, unsafe deserialization, or file upload risk is suspected.
- The user needs a security-focused review of input handling, not general form UX correctness.

## When not to use

- The concern is form UX correctness (client-side validation messages, field formatting); use `.claude/skills/form-validation-review`.
- The concern is API contract/design quality rather than security boundary; use `.claude/skills/api-design-review`.
- The task is a general implementation PR review; use `.claude/skills/code-review`.

## Composition and handoff rules

- Defer UX/form correctness to `.claude/skills/form-validation-review`.
- Defer API design judgment to `.claude/skills/api-design-review`.
- Defer implementation PR review to `.claude/skills/code-review`.

## Evidence access rule

Inspect the actual code path handling the untrusted input: where it enters, how it is parsed, validated, normalized, and used (query, filesystem, shell, deserializer, outbound request). If the code path, framework validation layer, or deployment context is unavailable, request exact file/route names and mark trust-boundary conclusions pending.

## Workflow

1. Map the input surface: entry points (routes, forms, file uploads, headers, deserialized bodies, message queues) and what each accepts.
2. Trace each input from entry to use: identify parsing, validation, normalization (or lack of it), and the sink (query, filesystem path, shell command, template, outbound request, deserializer).
3. Identify trust-boundary gaps: missing validation, unsafe parsing/deserialization, path traversal potential, SSRF-style forwarding, unrestricted file upload types/size/destination.
4. Produce a remediation plan (allowlist validation, safe parsing libraries, path canonicalization checks, upload restrictions, output encoding) without payload examples.
5. Recommend tests that verify the fix (boundary/edge cases, rejected malformed input, safe defaults) without constructing exploit strings.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to describe a working exploit or assume a framework auto-sanitizes.

## Questions to ask (only when necessary)

- Is there an existing input-validation library or middleware convention this should follow?

## Quality bar

- Every finding traces from actual code, not general injection-class speculation.
- Remediation is concrete (validation strategy, library, boundary check), not "be careful with input."
- No exploit payload, injection string, or evasion technique is included.

## Expected output format

```markdown
## Input validation security review: <scope>

**Input surface map**: <entry points and accepted input>
**Trust boundary findings**: <entry - sink - gap>
**Validation/normalization gaps**: <details>
**Remediation plan**: <steps>
**Test recommendations**: <boundary/edge cases>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Framework-trust assumption**: assuming a framework sanitizes input without checking its actual config/version behavior.
- **Payload demonstration**: including working exploit strings instead of describing the gap.
- **Generic injection lecture**: naming an injection class without tracing it to actual code.

More in `references/anti-patterns.md`.
