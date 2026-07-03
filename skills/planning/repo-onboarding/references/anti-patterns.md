# Repo Onboarding — Anti-patterns

## The disposable map
**Symptom**: a thorough, well-cited orientation summary — printed to chat, gone when the session ends. The next session pays the full exploration cost again.
**Why it fails**: onboarding's value compounds only if it persists; an ephemeral map makes the skill a slower version of what agents do by default.
**Instead**: write the map to `CLAUDE.md` or `docs/REPO-MAP.md`, stamped with date and commit, before handing off.

## Folder-name archaeology
**Symptom**: "`utils/` contains shared utilities; `core/` holds the core domain logic."
**Why it fails**: directory names routinely lie — `utils/` may hold the entire billing engine.
**Instead**: open at least one file per directory before describing it; if name and contents disagree, say so.

## Trusting the README
**Symptom**: reporting `npm run dev` as the dev command because the README says so; it fails because the repo moved to pnpm a year ago.
**Instead**: cross-check commands against `package.json` scripts / Makefile / CI config; prefer what CI runs.

## The 200-file deep dive
**Symptom**: an hour of reading before producing anything; context filled with file dumps.
**Instead**: cap the pass at entry points + representatives + recent files. Name what you skipped. Onboarding is a map, not a survey.

## Onboarding theater
**Symptom**: a beautiful summary, then the first code change ignores the observed conventions and error-handling idiom.
**Instead**: treat the conventions section as binding instructions for all later work in this repo.

## Guessing the test story
**Symptom**: "tests appear to use pytest" from seeing one `test_` file, when the suite actually runs under tox with required env vars.
**Instead**: find the invocation in CI or scripts and, when safe, run it once to prove it.

## Silent doc-code conflict resolution
**Symptom**: docs describe architecture A, code implements B; the summary quietly presents B.
**Instead**: report the conflict — it is a finding the team likely wants to know about.
