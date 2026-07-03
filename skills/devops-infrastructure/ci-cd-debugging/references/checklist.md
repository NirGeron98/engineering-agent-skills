# CI/CD Debugging - Checklist

## Evidence
- [ ] Workflow/run URL or run id recorded
- [ ] Branch, commit, event, runner, environment, and matrix entry recorded
- [ ] First failing log section captured or exact log request supplied
- [ ] Workflow config and called scripts/actions opened
- [ ] Recent relevant changes checked

## Pipeline boundary
- [ ] Failed stage/job/step identified
- [ ] Local vs CI command difference checked
- [ ] Dependency install and lockfile behavior checked
- [ ] Cache key, restore key, and cache-hit behavior checked
- [ ] Artifact paths and upload/download boundaries checked
- [ ] Branch/path conditions and environment gates checked
- [ ] Matrix-specific variables and exclusions checked

## Secrets and permissions
- [ ] Required secrets/variables identified without exposing values
- [ ] Token scope and protected environment rules checked where relevant
- [ ] Fork/PR permission restrictions checked where relevant
- [ ] Permission-only root cause handed to `iam-permissions-debugging`

## Safety and handoff
- [ ] Rerun vs real fix distinction stated
- [ ] Minimal fix plan names exact file or CI setting
- [ ] Verification/rerun plan is exact
- [ ] Pending evidence is clearly marked
