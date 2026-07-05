---
name: secrets-exposure-review
description: Review code, config, logs, commits, CI output, containers, or docs for exposed credentials, tokens, API keys, certificates, or private material. Use when a suspected or confirmed secret leak needs exposure findings, severity assessment, immediate containment checklist, rotation plan, and prevention recommendations. Defensive only.
---

# Secrets Exposure Review

## Goal

Turn a suspected or confirmed secret leak into an evidence-backed exposure assessment and safe remediation plan, without offensive or exploitation detail.

## When to use

- A credential, token, API key, certificate, or private material may be exposed in code, config, commits, logs, CI output, containers, or docs.
- The user needs to know how exposed a secret is, who could have seen it, and how to remediate.
- Rotation or containment planning is needed for a found secret.

## When not to use

- The task is general code review with no exposure suspicion; use `.claude/skills/code-review`.
- The concern is a vulnerable dependency, not a leaked credential; use `.claude/skills/dependency-vulnerability-triage`.
- Evidence suggests the secret was already used maliciously or access occurred; use `.claude/skills/security-incident-scoping`.

## Composition and handoff rules

- Defer general code review to `.claude/skills/code-review`.
- Defer dependency CVE work to `.claude/skills/dependency-vulnerability-triage`.
- Defer incident coordination to `.claude/skills/security-incident-scoping` when exposure may already be exploited: specifically, if access logs, audit logs, cloud provider logs, CI logs, or token usage records show the credential was queried, used, accessed from an unfamiliar location, or may have been used by an unauthorized actor. If there is no evidence of use yet, keep scope conclusions pending and continue exposure review, containment, rotation, and prevention planning here.

## Evidence access rule

Inspect the actual file, commit, log line, CI output, or config where the secret appears, including its history and exposure surface (public repo, CI logs, shared artifact, internal-only). If access to history, logs, or scope of exposure is unavailable, request exact commands or locations and mark exposure-scope and severity conclusions pending.

## Workflow

1. Confirm the finding: what looks like a secret, where it appears, and whether it is live/valid, expired, or a placeholder — without attempting to use or validate it against a live system.
2. Map exposure surface: commit history, branch visibility, CI logs, build artifacts, shared channels, public access, and how long it has been exposed.
3. Assess severity by what the credential grants access to, blast radius, and exposure duration/visibility.
4. Produce an immediate containment checklist (safe, read-only-first: identify scope before revoking, confirm owner, confirm no wider automated dependency breaks silently).
5. Produce a rotation/remediation plan and prevention recommendations (secret scanning, .gitignore, pre-commit hooks, secret manager migration).
6. Before final handoff, load `references/checklist.md`; consult `references/anti-patterns.md` when tempted to assume scope, skip rotation, or treat the finding as low-risk without evidence.

## Questions to ask (only when necessary)

- Who owns this credential and its rotation process?
- Is there an existing secret manager or vault this should move into?

## Quality bar

- Exposure scope and severity are evidence-based, not assumed.
- Containment steps are ownership-aware and do not silently break dependent systems.
- No exploitation, validation-against-live-system, or credential-use instructions are included.

## Expected output format

```markdown
## Secrets exposure review: <scope>

**Finding**: <what/where/type>
**Exposure surface**: <history/visibility/duration>
**Severity assessment**: <access granted/blast radius>
**Immediate containment checklist**: <steps with owner>
**Rotation/remediation plan**: <steps>
**Prevention recommendations**: <scanning/hooks/vault>
**Pending evidence**: <exact requests>
```

## Failure modes to avoid

- **Scope guessing**: declaring exposure "internal only" without checking actual visibility.
- **Rotation skipped**: treating removal from the latest commit as sufficient without rotating the credential.
- **Silent breakage**: revoking a credential without checking what depends on it.

More in `references/anti-patterns.md`.
