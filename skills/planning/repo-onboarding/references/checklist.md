# Repo Onboarding — Checklist

## Evidence gathering
- [ ] Read README / CLAUDE.md / CONTRIBUTING at repo root
- [ ] Monorepo: target package identified from workspace config; map scoped to it
- [ ] Stack identified from manifests (package.json / pyproject.toml / go.mod / …), not folder names
- [ ] Every entry point file opened and traced (server boot, CLI, jobs)
- [ ] 1–2 representative files opened per major source directory
- [ ] 2–3 recently changed files read for conventions (naming, errors, tests)
- [ ] Install / build / lint / test commands located in config or scripts
- [ ] Test suite (or subset) run if cheap and safe; baseline recorded

## Risk scan
- [ ] Generated or vendored code identified (do-not-edit zones)
- [ ] Migrations / schema files located
- [ ] Feature flags or environment-dependent behavior noted
- [ ] Very large, widely imported files flagged

## Output
- [ ] Every claim in the summary cites a file actually read
- [ ] Docs-vs-reality discrepancies listed (or "docs match code")
- [ ] Summary fits on one screen
- [ ] Uncovered areas explicitly listed, not silently omitted
- [ ] Map persisted to CLAUDE.md or docs/REPO-MAP.md — not only chat
- [ ] Persisted map stamped with date and commit it was verified against
