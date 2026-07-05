---
name: api-docs-review
description: Review API documentation, endpoint reference, OpenAPI specs, request/response examples, or SDK docs against implementation and contract evidence. Use when endpoint purpose, auth, request/response shape, errors, pagination, filtering, versioning, examples, and edge cases need an API documentation mismatch report and example fixes.
---

# API Docs Review

## Goal

Verify that API documentation accurately describes the implemented or intended contract, including request/response examples, errors, auth, compatibility, and edge cases.

## When to use

- API docs, OpenAPI specs, endpoint reference, or SDK docs need review.
- Examples may not match implementation or real contract behavior.
- The user needs missing or incorrect API documentation identified.

## When not to use

- The API design itself needs judgment; use `.claude/skills/api-design-review`.
- The task is implementation PR review; use `.claude/skills/code-review`.
- The README is the main target; use `.claude/skills/readme-review`.

## Composition and handoff rules

- Hand API contract design judgment to `.claude/skills/api-design-review`.
- Hand implementation review to `.claude/skills/code-review`.
- Hand README-level docs to `.claude/skills/readme-review`.

## Evidence access rule

Inspect the actual docs/spec, routes/controllers/handlers, schemas/types, tests, examples, auth middleware, SDK code, and generated artifacts before claiming mismatch. If runtime examples or generated docs cannot be checked, request exact output or commands and mark conclusions pending.

## Workflow

1. Scope endpoints, versions, consumers, docs source of truth, and generated vs handwritten docs.
2. Compare documentation to implementation or authoritative spec for purpose, auth, request fields, responses, errors, pagination, filtering, versioning, and edge cases.
3. Verify examples against schemas/tests or executable examples where possible.
4. Classify issues as missing docs, incorrect docs, ambiguous examples, contract mismatch, or design issue needing handoff.
5. Propose concrete doc edits and example fixes with evidence.
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to redesign the API, trust generated docs blindly, or skip error cases.

## Questions to ask (only when necessary)

- Which artifact is authoritative when spec, implementation, and SDK disagree?

## Quality bar

- Every mismatch cites docs plus implementation/spec evidence.
- Examples include realistic auth, error, pagination/filtering, and edge-case behavior where relevant.
- Design concerns are clearly separated from documentation defects.

## Expected output format

```markdown
## API documentation review: <API/scope>

**Evidence reviewed**: <docs/spec/routes/tests/examples>
**Mismatch report**: <doc claim - evidence - correction>
**Missing docs**: <endpoint/field/error/example>
**Example fixes**: <request/response/error examples>
**Design handoffs**: <items for api-design-review>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Spec worship**: trusting generated output without checking source or implementation.
- **Happy-path docs only**: documenting 200 responses while hiding auth, errors, limits, or pagination.
- **Design rewrite disguised as docs**: changing contract decisions without a design review.

More in `references/anti-patterns.md`.
