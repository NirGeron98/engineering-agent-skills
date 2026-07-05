# Secrets Exposure Review Checklist

- [ ] The exact file, commit, log, CI output, or config containing the finding was inspected.
- [ ] Whether the credential appears live, expired, or a placeholder is stated (without live validation attempts).
- [ ] Exposure surface (history, visibility, CI logs, artifacts, sharing) is mapped from evidence.
- [ ] Severity is based on access granted and blast radius, not assumption.
- [ ] Containment checklist has an owner and does not silently break dependents.
- [ ] Rotation plan is included, not just removal from the latest version.
- [ ] Prevention recommendations (scanning, hooks, vault migration) are included.
- [ ] Missing repo history, log access, or ownership info is marked pending.
- [ ] No exploitation, credential-use, or evasion instructions are present.
