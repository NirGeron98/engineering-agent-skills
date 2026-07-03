# Evaluating this repository

Sixty-three realistic scenarios for testing whether these skills route correctly and change agent behavior. Run each prompt in a session with all 63 skills installed (`.claude/skills/`), against a repo that fits the scenario. Judge against the pass/fail criteria — not against whether the answer "sounds good". Scenarios 43-52 cover the `security-review` and `observability` domains; scenarios 53-63 cover the `database` and `performance` domains.

Two things are under test in every scenario:

1. **Routing** — does the right skill load, and do the wrong ones stay out of the way?
2. **Behavior delta** — does the agent do something it demonstrably would not do without the skill (persist an artifact, request evidence instead of guessing, prove idempotency by double-run)?

---

## 1. Ambiguous repo onboarding task

**User prompt**: "I just cloned this repo and I need to add team-level permissions next week. Help me get oriented first."

**Expected skill**: `repo-onboarding`
**Acceptable secondary**: `task-breakdown` afterwards (for the permissions work), not instead.

**Expected behavior**: identifies stack from manifests, not folder names; opens entry points and representative files; verifies test commands from scripts/CI; calls out docs-vs-reality gaps; in a monorepo, scopes to the target package and says so; **persists the map to `CLAUDE.md` or `docs/REPO-MAP.md`** stamped with date and commit; lists what it didn't cover.

**Bad behavior**: orientation summary printed only to chat (the disposable map); claims like "`services/` contains the service layer" without opening files; repeating README commands unverified; reading 50+ files before producing anything.

**Pass/fail**: PASS requires (a) a persisted map file in the repo, (b) every architectural claim citing a file that was read, (c) at least one docs-vs-reality statement (even "docs match code"). FAIL if the map exists only in chat or any claim traces to a folder name.

---

## 2. Backend bug with no reproduction yet (production symptom)

**User prompt**: "Since this afternoon staging checkout requests are timing out. Prod is fine. Figure out what's going on." (Agent has repo access but no access to logs, dashboards, or deploy history.)

**Expected skill**: `service-debugging`
**Acceptable secondary**: `diagnose-bug` **only after** the failure is narrowed to a reproducible code defect.

**Expected behavior**: recognizes it lacks observability access and **emits exact evidence requests** (specific `kubectl`/log/deploy-history commands for the user to run and paste back); marks dependent conclusions as pending; pins onset to a timeline and enumerates candidate changes; diffs failing-env config against a healthy env; segments p50 vs p99; proposes read-only verification; any remediation comes with blast radius and replay analysis.

**Bad behavior**: summarizing logs it never saw (invented telemetry); jumping into application source for hours before checking env/config drift; proposing a restart or queue purge as a diagnostic step; "fixing" a config drift by patching application code.

**Pass/fail**: PASS requires (a) at least one explicit evidence request with the exact command/query, (b) zero claims about telemetry the agent didn't receive, (c) no state-mutating diagnostic. FAIL on any invented log line or metric.

---

## 3. Narrowed, reproducible bug

**User prompt**: "This test fails: `test_invoice_totals` — totals are off by one day for orders created near midnight UTC. It started after the timezone refactor. Find the cause and fix it."

**Expected skill**: `diagnose-bug`
**Acceptable secondary**: none (service-debugging must NOT trigger — the defect is local and reproducible).

**Expected behavior**: reproduces first; locates the failure boundary (last-correct → first-wrong); forms ≤3 falsifiable hypotheses and records rejections; consults `git log` for the refactor; **writes/observes the regression test failing before the fix**; minimal diff; searches for the same defect pattern elsewhere; handoff separates evidence from theory.

**Bad behavior**: patching the first suspicious date-handling line and re-running to see if the symptom moves; fixing the test's expectation to match the code; bundling refactors into the fix; declaring root cause at "plausible theory" confidence.

**Pass/fail**: PASS requires (a) the regression test demonstrably failed before the fix, (b) rejected hypotheses recorded, (c) a sibling-pattern search reported. FAIL if the fix landed before the cause was demonstrated.

---

## 4. Large PR code review

**User prompt**: "Review PR #214." (A 35-file PR: one schema migration, two new endpoints, a pagination change, plus ~25 files of mechanical renames and a lockfile.)

**Expected skill**: `code-review`
**Acceptable secondary**: hand-offs to `api-design-review` (contract shape) and `auth-flow-review` (authz depth) for the new endpoints — flagged, not silently absorbed.

**Expected behavior**: diffs against the PR's base branch; excludes lockfile/generated noise; **partitions by risk and says which areas got lighter coverage**; reads changed hunks in caller context; verifies each finding with a concrete failure scenario; spot-checks that new tests fail without the change; reports "checked and clean" areas; defers contract-shape and access-control depth to the specific skills by name.

**Bad behavior**: uniform shallow skim of all 35 files reported as a full review; vibe findings ("might be a race") with no scenario; 20 style nitpicks; "LGTM" with no record of what was examined.

**Pass/fail**: PASS requires (a) explicit risk partitioning or coverage statement, (b) every finding carrying file:line + failure scenario, (c) a checked-and-clean section. FAIL if the lockfile got review attention or any finding lacks a scenario.

---

## 5. API design review

**User prompt**: "Does this new `POST /organizations/{id}/invitations/bulk` endpoint design look right? Here's the proposed request/response." 

**Expected skill**: `api-design-review`
**Acceptable secondary**: `auth-flow-review` hand-off for the access-control depth on the new route.

**Expected behavior**: reads 3–5 existing endpoints first and judges against *their* conventions, citing them; checks who consumes the contract; hunts semantic breaking changes explicitly; verifies any authz claim against actual middleware/guards in code; checks pagination/idempotency/limits; classifies every change additive vs breaking.

**Bad behavior**: generic REST lecturing (HATEOAS, RFC 7807) disconnected from the service's own conventions; reviewing only the 200 response; trusting "admin-only" from the design doc without finding the guard; redesigning the resource model unasked.

**Pass/fail**: PASS requires (a) a named convention baseline from real endpoints, (b) authz enforcement verified in code or flagged as unverified, (c) explicit compatibility classification. FAIL if any consistency finding cites no existing endpoint.

---

## 6. Auth / IDOR review

**User prompt**: "We're adding document sharing. Can you check the documents API is actually protected properly?"

**Expected skill**: `auth-flow-review`
**Acceptable secondary**: `code-review` for non-auth implementation issues found along the way (flagged, not deep-dived).

**Expected behavior**: writes the intended access matrix first; **enumerates every route mechanically** (or resolvers, for GraphQL) — including batch/import/webhook/internal endpoints; traces object-level checks to the actual query predicate (`WHERE id = $1` with no owner/tenant predicate = IDOR); checks mass assignment on create/update calls; verifies all inspectable layers before declaring a gap and names uninspectable ones; recommends a regression test per confirmed gap.

**Bad behavior**: stopping at "it has requireAuth middleware"; sampling five prominent routes; declaring a gap that actually lives in a gateway/RLS layer without checking; reporting gaps with no scenario ("this looks insecure").

**Pass/fail**: PASS requires (a) an exhaustive route/resolver inventory with per-route guard status, (b) every finding carrying a concrete principal-request-obtains scenario, (c) layers-not-inspectable named. FAIL if the inventory is sampled or any "unprotected" claim skipped a visible enforcement layer.

---

## 7. Dataform BigQuery incremental model issue

**User prompt**: "Our `fct_payments` model in Dataform keeps producing duplicate rows after failed-run retries, and the BigQuery bill for it looks way too high. Fix it."

**Expected skill**: `dataform-bigquery-development`
**Acceptable secondary**: `data-quality-contracts` as a follow-up (assertion coverage), `db-to-dwh-pipeline` must NOT trigger (the data is already in BigQuery).

**Expected behavior**: reads `workflow_settings.yaml`/layer conventions first; checks the incremental config for `uniqueKey` (append semantics without it = the duplicate mechanism); checks the `when(incremental(), ...)` filter for lookback and partition pruning; verifies grain by query; enumerates dependents before changing anything; proves the fix with a dev-workspace run, a deliberate re-run (no dupes), a dry-run bytes comparison, and a dev-vs-prod output diff; requests commands from the user if it can't run `bq`/`dataform` itself.

**Bad behavior**: recommending `--full-refresh` as the standing fix (the eternal crutch); hardcoding table paths; asserting the fix works from reading the SQL without a re-run proof; ignoring the cost half of the question.

**Pass/fail**: PASS requires (a) the merge contract (uniqueKey + lookback) diagnosed explicitly, (b) re-run idempotency demonstrated or requested as a pending verification, (c) bytes-scanned before/after addressed. FAIL if the answer is only "add uniqueKey" with no verification path, or if full-refresh is the fix.

---

## 8. DB-to-DWH count mismatch

**User prompt**: "Our `orders` table in BigQuery has ~3% fewer rows than the Postgres source, and the gap is growing. The sync job says it succeeds every night."

**Expected skill**: `db-to-dwh-pipeline`
**Acceptable secondary**: `data-quality-contracts` as a follow-up (reconciliation checks that would have caught this).

**Expected behavior**: profiles before theorizing — and **profiles safely** (replica/off-peak, or hands the user exact SQL to run); checks the classic mechanisms in order of likelihood: `updated_at` not set by all write paths, knife-edge watermark losing late-committing transactions, hard deletes never propagated (which would cause the *opposite* sign — reasoning about gap direction is a good signal); verifies with targeted queries (e.g., find source rows absent from the warehouse and inspect their timestamps/write path); proposes a fix plus reconciliation checks wired into the pipeline; estimates backfill cost before running the repair.

**Bad behavior**: guessing "probably deletes" without checking gap direction; firing unbounded COUNT/checksum queries at the production primary midday; a repair backfill run with no cost estimate; fixing the gap without adding the reconciliation check that detects recurrence.

**Pass/fail**: PASS requires (a) profiling queries run or handed to the user before any theory is asserted, (b) the missing rows characterized (which rows, what they have in common), (c) reconciliation added as part of the fix. FAIL if a cause is asserted without a query result behind it.

---

## 9. Data quality incident / freshness breach

**User prompt**: "Yesterday the exec dashboard showed Tuesday's numbers because `fct_subscriptions` didn't refresh, and nobody noticed until the CFO asked. Make sure this can't happen silently again."

**Expected skill**: `data-quality-contracts`
**Acceptable secondary**: `dataform-bigquery-development` if the fix touches model assertions mechanics.

**Expected behavior**: identifies consumers and their dependencies (query logs/BI usage — or the documented fallback: grep BI repos, ask for a usage export, label unverified claims as assumptions); profiles arrival patterns before setting the freshness threshold; calibrates from observed p99 + margin, not imagination; sets a breach policy with teeth (this class of miss should page, not just Slack); **names an owning team/escalation path or flags its absence as a finding**; backtests against 30–90 days (the incident itself must fire in the backtest); forces one failure to prove the alert path; prices the checks (windowed/partition-scoped, not full-table scans).

**Bad behavior**: dropping in a generic `dbt source freshness` config with default thresholds; a threshold that would fire weekly on normal weekend dips; an alert into an unwatched channel with no owner; full-table checks on a large table with no cost thought; no backtest ("the check looks right").

**Pass/fail**: PASS requires (a) the threshold justified by profiled arrival data, (b) the backtest shown to catch yesterday's incident, (c) a named owner + tested alert path. FAIL if any threshold is unjustified by data or the alert path was never exercised.

---

## 10. Multi-layer feature planning (with plan mode active)

**User prompt**: "We need to add CSV export for the reports page — API, background job, and UI download. How would you approach this?" (Run once normally and once with the tool's built-in plan mode active.)

**Expected skill**: `task-breakdown`
**Acceptable secondary**: `repo-onboarding` first if the repo is unmapped — then task-breakdown, not instead.

**Expected behavior**: restates the task as an observable outcome; investigates before planning — **every plan step names a file actually opened** (investigation receipt); finds and cites the closest existing feature to mirror; sequences contracts (API shape, job schema) before consumers; each step carries its own verification; scope exclusions explicit; plan delivered somewhere durable (PR description, issue, or `docs/plans/<task>.md`), not only chat. With plan mode active: enriches the active plan with receipts and verification rather than producing a second, parallel plan.

**Bad behavior**: a generic to-do list ("1. Add endpoint 2. Add job 3. Add UI") citing no inspected files; steps with no verification method; no exclusions; plan lives only in chat; a duplicate plan alongside plan mode's own (plan-mode déjà vu).

**Pass/fail**: PASS requires (a) every step naming files actually inspected, (b) a durable plan artifact, (c) with plan mode active, one enriched plan — not two. FAIL if any step references a file the agent never opened, or the plan evaporates with the session.

---

## 11. Component design review on a reusable modal/table/form component

**User prompt**: "Before we roll out the new reusable `ConfirmModal` to the billing and admin flows, review the component API and design."

**Expected skill**: `component-design-review`
**Acceptable secondary**: `accessibility-review` for deep focus/dialog behavior; `code-review` only after component findings are complete.

**Expected behavior**: opens the component, existing modal/dialog precedents, at least two callers or all callers if fewer, tests/stories, and styling files; identifies component-owned vs caller-owned state; reviews props/events/slots/children, controlled/uncontrolled behavior, loading/error states, styling hooks, accessibility hooks, and testability; requests Storybook/browser/screenshot evidence if dialog layout or focus behavior cannot be rendered; loads `references/checklist.md` before handoff.

**Bad behavior**: generic component advice with no caller evidence; turning the modal into a design-system rewrite based on hypothetical future uses; declaring focus trap or scroll lock correct from source code alone; broad PR review that misses the component contract.

**Pass/fail**: PASS requires (a) findings cite component and caller evidence, (b) state ownership and public API are explicitly summarized, (c) visual/interactive claims are verified or marked pending with exact evidence request. FAIL if recommendations are generic or based on imagined consumers.

---

## 12. State management issue with duplicated derived state and stale server cache

**User prompt**: "The project dashboard shows stale totals after editing a task. I also see the same filter state in the URL, a store, and local component state. Untangle it."

**Expected skill**: `frontend-state-management`
**Acceptable secondary**: `api-integration-debugging` if network evidence is needed to prove the response shape; `frontend-performance-review` only if measured repeated renders/API calls are part of the symptom.

**Expected behavior**: inventories local, derived, URL, server/cache, global, form, and optimistic state with read/write locations; identifies one source of truth per value; names exact cache/query keys and mutation invalidation paths; traces the edit flow and route/filter flow; checks races and stale responses; avoids adding a global store unless real consumers require it; requests browser/test/network evidence when runtime timing cannot be reproduced.

**Bad behavior**: "move it to Redux/Pinia/service store" as the first answer; memoizing stale data; saying "invalidate the query" without naming keys; ignoring URL shareability; treating server cache state as ordinary local state.

**Pass/fail**: PASS requires (a) a state inventory table or equivalent, (b) exact cache keys/invalidation path named, (c) duplicated derived state removed or justified, (d) stale-after-mutation behavior verified or exact pending check supplied. FAIL if the fix globalizes state without evidence.

---

## 13. Form validation review with client/server mismatch and async uniqueness check

**User prompt**: "Review the workspace creation form. Client validation says the slug is valid, the server rejects it, and the async uniqueness check sometimes shows the wrong result."

**Expected skill**: `form-validation-review`
**Acceptable secondary**: `accessibility-review` for detailed error announcement/focus behavior; `api-design-review` if the server error contract itself must change.

**Expected behavior**: maps fields, defaults, required rules, endpoint/action, success and reset behavior; compares client schema/rules with server validation and error shape; traces touched/dirty state, disabled/loading state, double-submit prevention, async uniqueness debounce/cancellation, stale response handling, server error mapping, and input preservation; checks labels/error associations/focus; verifies invalid, async pass/fail, server rejection, success, and reset paths or requests exact evidence.

**Bad behavior**: stopping at "use shared schema"; treating client validation as authoritative; mapping server field errors only to a toast; disabling submit with no actionable error; ignoring stale async validation responses.

**Pass/fail**: PASS requires (a) client/server validation mismatch named precisely, (b) async uniqueness race handled, (c) server errors mapped to fields or justified as form-level, (d) double submit and reset behavior checked. FAIL if server rejection paths are not reviewed.

---

## 14. Accessibility review of a modal or dropdown

**User prompt**: "Audit the command palette modal for accessibility. I'm especially worried about keyboard and screen-reader behavior."

**Expected skill**: `accessibility-review`
**Acceptable secondary**: `component-design-review` only for public API changes needed to support accessibility; `form-validation-review` if form errors inside the modal become the focus.

**Expected behavior**: scopes the interaction path and states; inspects semantics, accessible names, labels, ARIA necessity, keyboard navigation, focus trap/return, Escape behavior, visible focus, dynamic announcements, contrast/reduced motion if relevant; runs or requests browser/DOM/screenshot/axe/manual keyboard evidence; treats automated checks as support, not proof; loads `references/checklist.md` before handoff.

**Bad behavior**: adding ARIA roles to non-native controls without keyboard behavior; declaring accessible because axe passes; no rendered DOM evidence for focus order; missing focus return after close; reporting issues without user impact.

**Pass/fail**: PASS requires (a) keyboard/focus behavior verified or exact pending evidence request, (b) every finding includes user impact and concrete fix, (c) source-only claims avoided for screen-reader/focus/contrast behavior. FAIL if the audit is automated-only.

---

## 15. RTL review of a Hebrew/English mixed dashboard

**User prompt**: "Review our Hebrew analytics dashboard. It mixes Hebrew labels, English campaign names, URLs, dollar amounts, date ranges, charts, and tables."

**Expected skill**: `rtl-ui-review`
**Acceptable secondary**: `accessibility-review` for keyboard/focus issues discovered during RTL checks; `component-design-review` for reusable table/chart API defects.

**Expected behavior**: identifies locale and `dir` mechanism; uses a mixed-direction fixture with Hebrew/English text, numbers, URLs, dates, currency, punctuation, long labels, and empty/error/loading states; inspects rendered screenshot/DOM/browser evidence; checks logical CSS, layout mirroring, popovers, forms, truncation, icons that should/should not mirror, tables, chart axes/legends/tooltips, numeric alignment, and locale formatting; marks visual conclusions pending if no rendered evidence exists.

**Bad behavior**: checking only `dir="rtl"`; hard-coding Hebrew-only assumptions instead of generic RTL/bidi rules; flipping every icon; ignoring punctuation and LTR token isolation; signing off from CSS source alone.

**Pass/fail**: PASS requires (a) mixed RTL/LTR fixture named, (b) rendered evidence or exact screenshot/browser request, (c) findings cover at least one bidi text/formatting concern and one layout/control concern. FAIL if the review is only page-level mirroring.

---

## 16. Frontend performance issue with slow route and repeated API calls

**User prompt**: "The reports route is slow and the Network tab shows the same summary API getting called repeatedly. Find the frontend performance problem."

**Expected skill**: `frontend-performance-review`
**Acceptable secondary**: `frontend-state-management` if cache key/state ownership is the root cause; `service-debugging` only after evidence proves backend latency or errors dominate.

**Expected behavior**: defines target route/interaction and metric; measures or requests production-build/network/performance/bundle evidence; reads route entry, imports, data-fetch hooks, cache keys, render boundaries, asset/font loading, and relevant components; classifies bottleneck as repeated API calls, data-fetch waterfall, bundle, render, hydration, cache, image/font, third-party, or backend latency; recommends targeted fix and re-measurement; does not recommend memoization blindly.

**Bad behavior**: jumping to `React.memo`/computed caching without measurement; optimizing bundle while repeated requests dominate; using dev-mode timing as production truth; blaming backend because requests are slow without comparing frontend waterfall and server timing.

**Pass/fail**: PASS requires (a) measurement or exact capture request, (b) bottleneck classification with evidence, (c) repeated API calls tied to exact cache/request construction or component behavior, (d) before/after verification plan. FAIL if optimization advice is intuition-only.

---

## 17. API integration bug where UI shows empty data despite successful backend response

**User prompt**: "The customers page is empty, but backend logs show `GET /api/customers` returns 200 with rows. Debug the frontend/backend integration."

**Expected skill**: `api-integration-debugging`
**Acceptable secondary**: `frontend-state-management` if cache/filtering is the cause; `service-debugging` only if network evidence proves the server behavior is wrong; `api-design-review` if the contract design is the real issue.

**Expected behavior**: captures or requests browser Network/HAR/curl evidence: URL, method, status, request payload, response body, headers, cookies/auth, CORS, and console errors; compares client adapter/generated types/API docs/backend handler where available; checks base URL/env vars, auth/cookies, request params, response envelope, parsing, empty-state logic, pagination, UI filters, loading/success transitions, cache keys, retries, and stale data; classifies ownership at the boundary and hands off if server-side or contract-design issue is proven.

**Bad behavior**: accepting backend logs as sufficient network evidence; blaming backend because the UI is empty; changing CORS without captured preflight failure; ignoring response envelope or client-side filters; fixing mocks while real API remains mismatched.

**Pass/fail**: PASS requires (a) concrete network evidence or exact HAR/request instructions, (b) request and response shape compared to client mapping, (c) boundary owner classified with evidence, (d) exact fix location or skill handoff named. FAIL if the answer relies only on backend logs or source-code inference.

---

## 18. CI/CD deploy job fails after dependency/cache change

**User prompt**: "GitHub Actions started failing on the deploy job after a dependency/cache change. The build passes locally."

**Expected skill**: `ci-cd-debugging`
**Acceptable secondary**: `diagnose-bug` only after the failing command is reproducible locally; `deployment-debugging` only if the deploy artifact succeeded but runtime behavior fails; `iam-permissions-debugging` if token scope or cloud permission is proven.

**Expected behavior**: identifies failing workflow/run/job/matrix entry; requests or inspects the first failing log section; checks recent workflow, lockfile, cache key, dependency, artifact, branch/path condition, secret, token, and runner changes; distinguishes rerun from real fix; produces an evidence table, minimal fix plan, and exact rerun/verification plan; loads `references/checklist.md` before handoff.

**Bad behavior**: declaring cache corruption without log evidence; clearing caches or rerunning until green as the fix; widening CI token permissions without principal/action/resource proof; debugging application code before isolating the failing CI command.

**Pass/fail**: PASS requires (a) failing log evidence or exact `gh`/CI log request, (b) failed stage/job boundary identified, (c) cache/dependency/artifact/condition/secrets considered where relevant, (d) rerun vs real fix stated. FAIL if it invents CI logs or treats a rerun as root cause.

---

## 19. Successful deployment returns 503 in staging

**User prompt**: "The deployment succeeded, but the service returns 503 in staging. Logs and runtime configuration are not directly available."

**Expected skill**: `deployment-debugging`
**Acceptable secondary**: `kubernetes-workload-debugging` if pod/workload evidence is central; `iam-permissions-debugging` if identity/permission denial is proven; `service-debugging` only after runtime/deployment evidence is healthy and app behavior remains broken.

**Expected behavior**: records service, environment, platform, deploy time, artifact/image version, and symptom; requests exact read-only deploy history, health, logs, runtime config, env vars, port, identity, ingress/routing, and rollback evidence; compares against a healthy environment or previous revision; classifies boundary as artifact, config, health, identity, network/routing, platform, app, or dependency; prefers rollback/mitigation before invasive changes.

**Bad behavior**: assuming repository config is deployed config; restarting/redeploying before read-only evidence; blaming code without runtime config/logs; no rollback candidate.

**Pass/fail**: PASS requires (a) exact runtime evidence commands or requests, (b) no cloud/log claims without evidence, (c) boundary classification with pending items, (d) rollback/mitigation considered before mutation. FAIL on invented logs or production mutation as diagnosis.

---

## 20. Terraform plan appears to replace production resources

**User prompt**: "Review this Terraform plan before apply. It appears to replace a production database/network/service account resource."

**Expected skill**: `terraform-plan-review`
**Acceptable secondary**: `iam-permissions-debugging` for permission root-cause work; `deployment-debugging` for post-apply runtime symptoms; `kubernetes-workload-debugging` for workload symptoms.

**Expected behavior**: refuses to mark safe without actual plan output; requests `terraform plan -out=tfplan` and `terraform show -no-color tfplan` if missing; summarizes creates/updates/deletes/replacements; inspects destructive, IAM, network, database/stateful, provider/module, lifecycle, state drift, secrets, environment, cost/quota, rollback, and state recovery risks; gives safe/safe-with-conditions/do-not-apply recommendation.

**Bad behavior**: approving apply from `.tf` source only; missing `-/+` replacement risk; treating production database or network replacement as routine; recommending apply without rollback/state notes.

**Pass/fail**: PASS requires (a) actual plan output reviewed or exact plan command requested, (b) every replacement/delete high-risk item called out, (c) IAM/network/stateful/state risk considered, (d) recommendation includes conditions and rollback/state notes. FAIL if it says safe without seeing the plan.

---

## 21. Event trigger gets Cloud Run 403

**User prompt**: "Eventarc or a storage trigger is calling Cloud Run, but Cloud Run returns 403. Fix the permission issue safely."

**Expected skill**: `iam-permissions-debugging`
**Acceptable secondary**: `deployment-debugging` after permission root cause if service identity/runtime config needs validation; `ci-cd-debugging` if the failing identity is a CI token; `kubernetes-workload-debugging` if Kubernetes RBAC and workload state are central.

**Expected behavior**: captures exact 403/error evidence; identifies effective principal, action, resource, scope, and policy boundary; distinguishes trigger/runtime identity from human identity; requests audit logs, trigger/service account config, and Cloud Run IAM policy if missing; builds a principal/action/resource matrix; recommends least-privilege invoker or equivalent permission only after principal proof; includes verification command and risk notes.

**Bad behavior**: granting project owner/editor; assuming the developer identity is the caller; skipping audit logs/policy evidence; changing deployment code to fix an IAM denial.

**Pass/fail**: PASS requires (a) principal/action/resource matrix, (b) exact evidence request or policy/audit proof, (c) least-privilege fix, (d) verification command. FAIL if broad admin is the first fix or principal is guessed.

---

## 22. Kubernetes deployment stuck after rollout

**User prompt**: "A Kubernetes deployment is stuck in CrashLoopBackOff or Pending after the latest rollout."

**Expected skill**: `kubernetes-workload-debugging`
**Acceptable secondary**: `deployment-debugging` if artifact/platform deployment evidence is central but Kubernetes state is not; `iam-permissions-debugging` for permission-only root causes; `service-debugging` or `diagnose-bug` only after pod/runtime health is proven.

**Expected behavior**: records context, namespace, workload, rollout time, and version; starts with read-only `kubectl get`, `describe`, `logs`, `logs --previous`, `events`, and rollout history; checks image pull, scheduling, init containers, exit codes, probes, requests/limits, node constraints, ConfigMaps/Secrets, service account/RBAC, service selectors/endpoints, ingress/gateway, DNS, and NetworkPolicy where relevant; produces likely and ruled-out causes, safe fix, verification, and rollback notes.

**Bad behavior**: deleting pods as diagnosis; changing probes before logs/startup/dependency evidence; blaming app code while pods are Pending or unready; live-editing production manifests without rollback.

**Pass/fail**: PASS requires (a) read-only Kubernetes evidence or exact commands, (b) pod lifecycle and traffic path checked where relevant, (c) likely and ruled-out causes evidence-backed, (d) no mutation without impact and rollback. FAIL if pod deletion is the first diagnostic step.

---

## 23. Production error rate spikes after release

**User prompt**: "Production error rate spiked after a release. We need a triage runbook and mitigation plan."

**Expected skill**: `incident-triage-runbook`
**Acceptable secondary**: `deployment-debugging`, `service-debugging`, `ci-cd-debugging`, `terraform-plan-review`, `iam-permissions-debugging`, or `kubernetes-workload-debugging` only for narrowed root-cause tracks after triage structure exists.

**Expected behavior**: establishes severity, status, impact, owner, affected systems, and next update time; builds timeline from alerts, onset, release, recent changes, mitigations, and reports; requests or inspects metrics/logs/traces/deploy history; lists hypotheses and unknowns separately from facts; prioritizes rollback/mitigation and evidence preservation; writes a neutral communication update and follow-up list.

**Bad behavior**: declaring root cause before timeline/evidence; inventing metrics or customer impact; deep debugging while a safe rollback is available; blameful language.

**Pass/fail**: PASS requires (a) triage runbook with timeline and impact status, (b) exact evidence requests for missing telemetry, (c) immediate mitigation/rollback decision path, (d) neutral communication update. FAIL on invented telemetry or root-cause certainty without evidence.

---

## 24. Production deployment readiness with rollback and monitoring

**User prompt**: "Review whether tonight's production deployment is safe to proceed, including rollback and monitoring."

**Expected skill**: `release-and-rollback-readiness`
**Acceptable secondary**: `code-review` for PR implementation review; `api-design-review` for API compatibility risk; `terraform-plan-review` for infrastructure plan risk; `kubernetes-workload-debugging` for workload readiness; `incident-triage-runbook` if an active incident exists.

**Expected behavior**: defines release scope, changed services/resources, environment, owners, deploy sequence, CI status, artifact versions, feature flags, config changes, migrations, infrastructure changes, smoke tests, health checks, alerts, dashboards, observation window, rollback path, compatibility, no-go triggers, and post-release verification; produces go/go-with-conditions/no-go decision; blocks unconditional approval if rollback or monitoring evidence is missing.

**Bad behavior**: saying "CI is green, ship it"; approving without rollback; ignoring migration compatibility or infrastructure risk; no monitoring/alert owner.

**Pass/fail**: PASS requires (a) readiness report with go/no-go decision, (b) rollback path or explicit rollback-impossible risk, (c) monitoring and post-release checks, (d) pending evidence blocks unconditional approval. FAIL if release is marked ready without rollback and monitoring.

---

## 25. Architecture review for monolith-to-service proposal

**User prompt**: "Review the proposed architecture for moving a monolith feature into a separate service. We have a rough diagram and partial repo access."

**Expected skill**: `architecture-review`
**Acceptable secondary**: `api-design-review` for API contract detail; `release-and-rollback-readiness` for rollout safety; `terraform-plan-review` for infrastructure plan risk; `db-to-dwh-pipeline` if the split is primarily a data pipeline problem.

**Expected behavior**: inspects the diagram, proposal, available repo files, interfaces, current module responsibilities, data flow, runtime dependencies, tests, and deployment/runtime docs where available; separates diagram claims from verified code evidence; covers boundaries, ownership, failure modes, scalability, operability, testability, high-level security/privacy implications, risks, alternatives, and tradeoffs; requests exact missing evidence for dependency graph, ADRs, runtime data, or unavailable repo areas; produces an architecture review report.

**Bad behavior**: generic microservices advice; trusting the rough diagram as truth; recommending a rewrite or extraction without migration risk; doing PR-level code review instead of architecture review; claiming runtime dependencies or scaling behavior without evidence.

**Pass/fail**: PASS requires (a) evidence-backed component/data-flow/dependency map or exact evidence requests, (b) tradeoffs tied to constraints, (c) risks and open questions separated from facts, (d) specialist handoffs named where needed. FAIL if the review is architecture astronomy without repo or document evidence.

---

## 26. Architecture decision record for event-driven decision

**User prompt**: "We decided to use an event-driven approach instead of direct synchronous calls. Draft an ADR with tradeoffs and revisit triggers."

**Expected skill**: `architecture-decision-record`
**Acceptable secondary**: `architecture-review` if the decision is not understood; `task-breakdown` for implementation planning; `refactor-strategy` if migration sequencing is the main need.

**Expected behavior**: finds existing ADR conventions if present; gathers decision context, problem statement, constraints, options, chosen approach, consequences, tradeoffs, rejected alternatives, owners, follow-up actions, and evidence links; labels missing history or assumptions as pending; includes a concrete revisit trigger; produces an ADR draft or update.

**Bad behavior**: selling the chosen option while omitting alternatives; inventing decision history; writing an implementation task list instead of an ADR; no revisit trigger; no owner or evidence links.

**Pass/fail**: PASS requires (a) ADR format with context, decision, options, consequences, rejected alternatives, revisit trigger, and evidence links, (b) missing facts marked pending, (c) existing ADR convention followed or absence noted. FAIL if the ADR hides tradeoffs or backfills certainty.

---

## 27. Module boundary review for billing imports

**User prompt**: "The billing module imports user, subscription, email, and reporting internals. Review whether the module boundaries are healthy."

**Expected skill**: `module-boundary-review`
**Acceptable secondary**: `dependency-and-coupling-review` for deeper graph/cycle analysis; `component-design-review` if a frontend component boundary is the real concern; `service-debugging` only for runtime behavior.

**Expected behavior**: inspects billing module contents, imports, exports, public interfaces, callers, tests, ownership/domain docs, and deployment boundaries if relevant; classifies responsibilities; identifies leaked abstractions, shared utility sprawl, unstable boundaries, and test seams; proposes boundary changes with safe migration steps and verification.

**Bad behavior**: judging boundary health from folder names; recommending a broad module split by taste; adding interfaces while hidden coupling remains; performing generic code review; ignoring callers and tests.

**Pass/fail**: PASS requires (a) concrete import/caller/interface evidence, (b) responsibilities classified, (c) boundary violations tied to impact, (d) safe migration steps. FAIL if recommendations are generic cleanup with no evidence.

---

## 28. Dependency and coupling review for circular imports and shared utilities

**User prompt**: "Our packages have circular imports and shared utilities are growing. Map the coupling risk and propose a safer direction."

**Expected skill**: `dependency-and-coupling-review`
**Acceptable secondary**: `module-boundary-review` for responsibility questions; `refactor-strategy` for migration planning; `code-review` for PR-level defects.

**Expected behavior**: scopes packages/layers; inspects manifests, imports, dependency graph output or exact requested commands, build/test config, public APIs, callers, and shared utilities; maps dependency direction, cycles, hidden runtime/config coupling, framework leakage, and blast radius; ranks high-risk edges and proposes decoupling steps with verification.

**Bad behavior**: drawing a graph with no risk ranking; saying "add interfaces" as the fix; treating all shared utilities as equally bad; banning imports with no migration path; inferring coupling from directory names.

**Pass/fail**: PASS requires (a) coupling map from concrete edges or exact graph requests, (b) high-risk dependencies ranked, (c) decoupling strategy with verification, (d) deferred coupling justified. FAIL if coupling is hidden behind interfaces without reducing blast radius.

---

## 29. Technical debt triage from noisy complaint list

**User prompt**: "We have a long list of tech debt complaints. Prioritize what to fix first and what to ignore for now."

**Expected skill**: `technical-debt-triage`
**Acceptable secondary**: `refactor-strategy` after a specific debt item is selected; `task-breakdown` for issue breakdown; `architecture-review` for structural review if debt points to system-wide architecture.

**Expected behavior**: builds a debt inventory from the list plus inspectable issues, TODOs, incidents, flaky tests, churn, code hotspots, review pain, and ownership where available; scores impact, frequency, user/business impact, engineering cost, risk, blast radius, cost of delay, and dependencies; produces prioritized next actions, defer/ignore list with reasons, and suggested tracking issues; requests exact missing evidence.

**Bad behavior**: restating the complaint list; prioritizing by taste or loudest complaint; trying to fix everything; turning the task into refactor planning before ranking; no defer/ignore list.

**Pass/fail**: PASS requires (a) prioritized debt list with evidence or pending evidence, (b) explicit defer/ignore list, (c) suggested next actions or tracking issues, (d) impact/risk/cost reasoning. FAIL if the output is just a cleanup backlog.

---

## 30. Refactor strategy for core domain model rename

**User prompt**: "We need to rename and restructure a core domain model used across API, jobs, tests, and UI without breaking behavior."

**Expected skill**: `refactor-strategy`
**Acceptable secondary**: `diagnose-bug` if a behavior defect must be rooted first; `code-review` for implementation review after a PR exists; `technical-debt-triage` if prioritization is not settled; `architecture-decision-record` if the refactor depends on a durable decision.

**Expected behavior**: inspects current behavior, callers, tests, contracts, schemas, jobs, UI usage, deployment/runtime surfaces, and compatibility constraints; defines characterization tests; plans incremental migration slices with adapters/dual paths/flags where needed; includes rollback/abort criteria, verification per slice, ownership, and cleanup plan; requests exact missing evidence rather than guessing.

**Bad behavior**: big-bang rename; changing behavior while claiming refactor only; no characterization tests; ignoring API/jobs/UI consumers; cleanup bundled with risky migration; no rollback or compatibility strategy.

**Pass/fail**: PASS requires (a) current behavior and consumers evidenced or requested, (b) characterization test plan, (c) incremental slices with verification and rollback/abort criteria, (d) cleanup separated from migration. FAIL if the plan is a rewrite or global rename with no behavior-preservation strategy.

---

## 31. Test strategy for risky feature

**User prompt**: "We are adding account export with API, background job, storage, and UI download. Plan the tests so we cover the real risk without making CI slow."

**Expected skill**: `test-strategy`
**Acceptable secondary**: `tdd-workflow` for implementation slices afterwards; `task-breakdown` if the feature scope is still unclear; `e2e-test-debugging` and `flaky-test-debugging` only for actual failing tests.

**Expected behavior**: inspects feature surface, existing tests, contracts, CI commands, similar export flows, and risk evidence; builds a risk matrix; chooses unit/integration/contract/e2e/manual checks by risk; names what not to test yet; addresses CI runtime and ownership; requests exact evidence if CI/test data is missing.

**Bad behavior**: generic "add unit, integration, and e2e tests"; targeting coverage percentage; E2E-testing every branch; no CI cost or owner.

**Pass/fail**: PASS requires (a) risk matrix, (b) layer recommendations tied to evidence, (c) tests not worth adding, (d) commands or pending evidence requests. FAIL if the output is a generic coverage plan.

---

## 32. TDD workflow for regression fix

**User prompt**: "Fix this discount rounding bug, but write the regression test first and keep the change small."

**Expected skill**: `tdd-workflow`
**Acceptable secondary**: `diagnose-bug` first only if the root cause is not understood; `legacy-characterization-tests` if behavior must be captured before the fix.

**Expected behavior**: identifies public behavior and closest existing tests; writes or proposes a minimal failing test; observes or requests the red result before implementation; makes minimal implementation; verifies focused and relevant broader tests; refactors only after green.

**Bad behavior**: patching implementation before red; testing private helpers; accepting a test that already passes; bundling refactor into the fix.

**Pass/fail**: PASS requires (a) first failing test, (b) red result observed or exact command requested, (c) minimal implementation step after red, (d) verification summary. FAIL if the fix precedes the failing test.

---

## 33. Legacy characterization before refactor

**User prompt**: "This invoice renderer is legacy and poorly tested. Capture current behavior before we refactor it."

**Expected skill**: `legacy-characterization-tests`
**Acceptable secondary**: `refactor-strategy` after behavior capture; `diagnose-bug` if a defect must be rooted first; `test-strategy` for broader coverage planning.

**Expected behavior**: inspects current behavior, callers, outputs, fixtures, tests, side effects, and stable seams; separates current behavior from desired future behavior; recommends first characterization tests; handles golden/snapshot tests cautiously; requests sample outputs if unavailable.

**Bad behavior**: starting with a refactor; snapshotting huge unstable output; silently preserving or fixing suspected bugs; inventing legacy behavior.

**Pass/fail**: PASS requires (a) behavior inventory, (b) safest seams, (c) first tests with inputs/expected current outputs, (d) pending unknowns. FAIL if behavior changes before capture.

---

## 34. Integration test design for service boundary

**User prompt**: "Design integration tests for order creation across API, database, and queue publication."

**Expected skill**: `integration-test-design`
**Acceptable secondary**: `api-design-review` for contract shape; `db-to-dwh-pipeline` if pipeline behavior is central; `e2e-test-debugging` only for browser/user-flow failures.

**Expected behavior**: defines boundary under test; inspects both sides, contracts, fixtures, cleanup, existing harness, and CI constraints; chooses real vs fake dependencies with rationale; covers idempotency, retries, persistence, cleanup, and diagnostics; emits commands or pending evidence requests.

**Bad behavior**: mocking the queue/API/DB boundary being tested; no teardown; no CI cost; tests that only prove mocks.

**Pass/fail**: PASS requires (a) boundary and dependency strategy, (b) fixture/data plan, (c) reliability risks, (d) verification commands. FAIL if it mocks away the integration.

---

## 35. E2E test failure with missing artifacts

**User prompt**: "The checkout Playwright test fails in CI on the payment screen. It passes locally, and I only pasted the assertion error."

**Expected skill**: `e2e-test-debugging`
**Acceptable secondary**: `api-integration-debugging` if frontend/API evidence becomes central; `service-debugging` if backend runtime failure is proven; `flaky-test-debugging` if the issue is a general flake pattern.

**Expected behavior**: does not infer browser/network state from the assertion alone; requests exact trace/video/screenshot/console/network/HAR/test log evidence and reproduction command; checks selectors, waits, auth/session, API responses, test data, environment drift, and retry policy; classifies root cause with pending conclusions.

**Bad behavior**: adding sleeps immediately; declaring visual state from code; treating retry success as fix; blaming backend without network evidence.

**Pass/fail**: PASS requires (a) browser/runtime evidence or exact artifact requests, (b) root-cause classification with pending items, (c) ruled-out causes or evidence plan, (d) verification command. FAIL on source-only browser claims.

---

## 36. Flaky test in CI

**User prompt**: "`test_cache_expiry` fails once or twice a day in CI but passes locally. Stabilize it."

**Expected skill**: `flaky-test-debugging`
**Acceptable secondary**: `ci-cd-debugging` if runner/cache configuration is proven central; `diagnose-bug` if the failure becomes reproducible; `e2e-test-debugging` for browser-specific artifacts.

**Expected behavior**: gathers repeated pass/fail evidence, seeds, test order, time, parallelism, shared state, external service calls, retries, and environment differences; records hypotheses and rejections; separates quarantine/retry containment from repair; defines repeated-run verification.

**Bad behavior**: increasing timeout and stopping; one green rerun as proof; retry as fix; ignoring order/parallelism.

**Pass/fail**: PASS requires (a) failure pattern or exact repeated-run request, (b) hypotheses with rejected causes, (c) stabilization plan, (d) verification loop. FAIL if retries are the primary fix.

---

## 37. README review for broken setup

**User prompt**: "Review this README. New contributors say the setup instructions don't work."

**Expected skill**: `readme-review`
**Acceptable secondary**: `repo-onboarding` if the repo cannot be understood from docs and manifests; `developer-onboarding-docs` if the task expands beyond README.

**Expected behavior**: checks README against manifests, scripts, CI, env examples, docs, entry points, examples, and tests; verifies or requests install/run/test command output; identifies stale claims and missing sections; proposes concrete structure and edits.

**Bad behavior**: rewriting marketing copy; inventing setup commands; trusting existing README commands; turning README into a full manual.

**Pass/fail**: PASS requires (a) repo evidence for claims, (b) command verification or exact requests, (c) missing/stale section list, (d) specific edits or patch plan. FAIL if the review is generic docs advice.

---

## 38. API documentation review

**User prompt**: "Review the OpenAPI docs for our invitations endpoints before publishing."

**Expected skill**: `api-docs-review`
**Acceptable secondary**: `api-design-review` for contract design concerns; `code-review` for implementation PR defects.

**Expected behavior**: compares docs/spec to routes, handlers, schemas, tests, auth middleware, SDK examples, and generated artifacts; verifies request/response/error/auth/pagination/filtering/versioning docs; separates doc defects from design issues; requests generated output or runtime examples if missing.

**Bad behavior**: trusting generated docs blindly; reviewing only 200 responses; redesigning the API; examples that do not match schemas.

**Pass/fail**: PASS requires (a) mismatch report citing docs and implementation/spec, (b) missing error/auth/example coverage, (c) concrete example fixes, (d) design handoffs separated. FAIL if no implementation/spec evidence is used.

---

## 39. Runbook for recurring job alert

**User prompt**: "Write a runbook for the nightly import job failure alert. It should be safe for on-call to follow."

**Expected skill**: `runbook-writer`
**Acceptable secondary**: `incident-triage-runbook` for an active incident; `release-and-rollback-readiness` for deployment readiness; `kubernetes-workload-debugging` for Kubernetes-specific diagnosis.

**Expected behavior**: inspects alert, existing docs, dashboards/logs, scheduler/job config, ownership, rollback/mitigation docs, and prior incidents; starts with read-only checks; each command has expected output; mutation/rerun/backfill steps have guardrails, approval, validation, escalation, and cleanup; missing access is requested exactly.

**Bad behavior**: first step is rerun/restart/delete; commands without interpretation; no escalation; assuming production access.

**Pass/fail**: PASS requires (a) read-only evidence steps first, (b) command expectations, (c) guarded mitigation/rollback, (d) escalation and validation. FAIL if it starts with mutation.

---

## 40. Handoff for paused bug fix

**User prompt**: "I need to stop here. Write a handoff so another engineer can continue this bug fix."

**Expected skill**: `handoff-documentation`
**Acceptable secondary**: none as a replacement; use after the relevant diagnosis/review skill has done the work.

**Expected behavior**: inspects diff/status, tests, logs, notes, files touched, decisions, and issue/PR context; separates done, pending, blocked, risks, and unresolved questions; includes failed checks and next steps; requests missing evidence rather than inventing status.

**Bad behavior**: raw transcript dump; hiding failed tests; saying complete with pending verification; no resume point.

**Pass/fail**: PASS requires (a) status and resume point, (b) evidence/decisions, (c) verification run/not run, (d) next-step checklist. FAIL if the handoff overstates completion.

---

## 41. Changelog from commit range

**User prompt**: "Write the changelog for everything since v1.4.0."

**Expected skill**: `changelog-writer`
**Acceptable secondary**: `release-and-rollback-readiness` for go/no-go decisions; `code-review` for PR correctness; `handoff-documentation` for internal continuation notes.

**Expected behavior**: inspects diff, commits, PRs/issues if available, tags, migration notes, docs, and tests; groups features, fixes, breaking changes, migrations, deprecations, internal changes, and known issues; traces entries to evidence; requests missing commit range or PR list.

**Bad behavior**: writing from memory; burying breaking changes; presenting internal refactors as user value; including unreleased speculation.

**Pass/fail**: PASS requires (a) evidence source, (b) user-visible and internal changes separated, (c) breaking/migration notes explicit, (d) pending evidence marked. FAIL if no diff/commit evidence is used.

---

## 42. Developer onboarding docs for new contributors

**User prompt**: "Create developer onboarding docs so a new engineer can make their first contribution to this repo."

**Expected skill**: `developer-onboarding-docs`
**Acceptable secondary**: `repo-onboarding` if repo understanding is missing; `readme-review` for README-specific fixes; `architecture-review` for architecture-specific concerns.

**Expected behavior**: inspects README, docs, repo structure, manifests, scripts, tests, CI, architecture notes, deployment basics, ownership, and recent contribution patterns; builds a concise repo map, first-day path, setup verification checklist, workflows, troubleshooting, ownership, and contribution path; marks command/ownership gaps pending.

**Bad behavior**: onboarding encyclopedia; unverified setup path; no ownership/escalation; role-first overload.

**Pass/fail**: PASS requires (a) repo evidence, (b) first-day path with commands or pending requests, (c) repo map and workflows, (d) ownership/troubleshooting gaps. FAIL if it is generic onboarding advice.

---

## 43. Secrets exposure in a commit and CI logs

**User prompt**: "We found what looks like an API key in a commit and maybe in CI logs. Review exposure and remediation."

**Expected skill**: `secrets-exposure-review`
**Acceptable secondary**: `security-incident-scoping` only if evidence suggests the key was already used; `dependency-vulnerability-triage` not applicable here.

**Expected behavior**: inspects the actual commit/file/log location; maps exposure surface (history, visibility, CI retention); assesses severity from access granted, not assumption; produces an owner-aware containment checklist and a rotation plan, not just removal from the latest commit; requests repo visibility and CI log access if unknown.

**Bad behavior**: declaring the secret "not exposed" without checking repo visibility; treating removal from the latest commit as sufficient; skipping rotation; including exploitation/validation-against-live-system steps.

**Pass/fail**: PASS requires (a) exposure surface based on evidence, (b) rotation plan present, (c) no live credential validation attempted. FAIL if rotation is skipped or scope is assumed.

---

## 44. Dependency scanner CVE triage

**User prompt**: "Our dependency scanner flagged a critical CVE in a package we use. Triage risk and next steps."

**Expected skill**: `dependency-vulnerability-triage`
**Acceptable secondary**: `release-and-rollback-readiness` for rollout risk of the fix; `code-review` for the fix PR itself.

**Expected behavior**: checks the project's actual resolved version against the affected range; assesses reachability by searching for actual usage of the vulnerable function/module; ranks risk by reachability and exposure, not CVSS score alone; proposes an upgrade/mitigation plan with verification steps.

**Bad behavior**: ranking urgency purely on CVSS score; recommending a blind major-version bump; describing how the vulnerability could be exploited.

**Pass/fail**: PASS requires (a) reachability evidence or explicit pending request, (b) risk ranking beyond raw CVSS score, (c) no exploit description. FAIL if severity is decided from the advisory alone.

---

## 45. File upload input validation review

**User prompt**: "Review this file-upload endpoint for unsafe input handling and validation gaps."

**Expected skill**: `input-validation-security-review`
**Acceptable secondary**: `form-validation-review` for client-side UX only; `code-review` for the general PR.

**Expected behavior**: maps the input surface; traces the filename/content from entry to the storage sink; flags path traversal, content-type, and size-limit gaps found in actual code; proposes allowlist-based remediation; recommends boundary tests without literal exploit payloads.

**Bad behavior**: assuming the framework sanitizes filenames without checking; including a working traversal payload; a generic "validate your input" recommendation with no specific fix.

**Pass/fail**: PASS requires (a) findings traced to actual code, (b) concrete remediation, (c) no exploit payload. FAIL if framework safety is assumed without evidence.

---

## 46. Internal admin API access boundary review

**User prompt**: "Review whether this internal admin API has safe access boundaries across user roles and service accounts."

**Expected skill**: `access-control-boundary-review`
**Acceptable secondary**: `auth-flow-review` for user-facing login/session behavior; `iam-permissions-debugging` for cloud IAM specifically.

**Expected behavior**: maps the privilege model from actual role/permission code; checks whether resource-specific actions verify ownership, not just existence; assesses confused-deputy risk for service-to-service calls; recommends narrowing scope rather than broadening it.

**Bad behavior**: trusting a function named `checkAccess` without reading its logic; recommending a wildcard/broader role to resolve an access error; skipping the confused-deputy question entirely.

**Pass/fail**: PASS requires (a) boundary claims traced to code, (b) ownership vs existence distinguished, (c) remediation narrows rather than widens access. FAIL on any broad-permission-fix recommendation.

---

## 47. Suspected token compromise scoping

**User prompt**: "We suspect a token was used unexpectedly from an unfamiliar location. Scope the incident."

**Expected skill**: `security-incident-scoping`
**Acceptable secondary**: `incident-triage-runbook` if it broadens into general incident coordination; `runbook-writer` if a durable runbook is requested afterward.

**Expected behavior**: separates the known fact (anomalous login) from the unconfirmed assumption (malicious use); produces an evidence preservation checklist before any containment step; proposes containment (revoke/rotate/force re-auth) with approval and rollback framing; defines escalation path and follow-up investigation plan.

**Bad behavior**: declaring the account compromised outright; revoking the session before capturing evidence; containment steps with no owner or approval.

**Pass/fail**: PASS requires (a) known vs unknown facts separated, (b) evidence preserved before containment, (c) owned/approved containment options. FAIL if containment precedes evidence preservation.

---

## 48. Noisy alerts that still miss real incidents

**User prompt**: "Our alerts are noisy, but we still miss real incidents. Review metrics and alert quality."

**Expected skill**: `metrics-and-alerts-review`
**Acceptable secondary**: `slo-definition-review` if the conversation shifts to defining what "good" means; `dashboard-signal-audit` for dashboard-specific findings.

**Expected behavior**: inventories actual alert definitions and thresholds; uses paging history to identify noisy, low-action alerts and gaps where real incidents had no alert; recommends thresholds backed by historical data; flags ownership/escalation gaps.

**Bad behavior**: accepting noisy alerts as normal without flagging them; recommending a new threshold with no historical data; declaring coverage complete without checking real incident history.

**Pass/fail**: PASS requires (a) noise findings from paging history, (b) missing-coverage check against known failure modes, (c) evidence-backed thresholds. FAIL if thresholds are guessed.

---

## 49. Noisy, context-poor logs blocking debugging

**User prompt**: "Debugging this service is hard because logs are noisy and missing request context. Review log signal quality."

**Expected skill**: `log-signal-quality-review`
**Acceptable secondary**: `service-debugging` if it shifts into diagnosing a specific failure; `secrets-exposure-review` if sensitive data is found logged.

**Expected behavior**: samples actual logs across normal and failure conditions; flags specific noisy/low-value lines and missing correlation IDs; checks for sensitive-data logging risk without treating it as a confirmed incident; recommends specific log point changes with before/after examples.

**Bad behavior**: describing typical logging problems without inspecting real samples; recommending "add more logging" broadly; treating a sensitive-data-in-logs finding as a full incident response task.

**Pass/fail**: PASS requires (a) findings from actual log samples, (b) specific recommended changes, (c) correlation-ID gap explicitly checked. FAIL if findings are generic and not evidenced.

---

## 50. Slow checkout request trace diagnosis

**User prompt**: "We have distributed traces for a slow checkout request. Diagnose where latency is coming from."

**Expected skill**: `trace-based-latency-diagnosis`
**Acceptable secondary**: `service-debugging` if it broadens into general runtime debugging; `skills/performance/hot-path-profiling-review` if it narrows into code-level profiling.

**Expected behavior**: inspects actual trace/span data; builds a latency breakdown identifying the largest span or gap; flags unexplained gaps as missing-instrumentation risk rather than settled; records ruled-out causes; proposes next verification steps (add instrumentation, compare traces).

**Bad behavior**: naming a bottleneck (e.g., "the database") without span evidence; treating one trace as fully representative without a baseline; ignoring an unexplained timing gap.

**Pass/fail**: PASS requires (a) bottleneck hypothesis backed by span evidence, (b) missing-span gaps explicitly flagged, (c) ruled-out causes recorded. FAIL if a cause is asserted without span evidence.

---

## 51. Defining practical SLIs/SLOs for an API

**User prompt**: "Define practical SLIs/SLOs for this API based on user experience."

**Expected skill**: `slo-definition-review`
**Acceptable secondary**: `metrics-and-alerts-review` for alert mechanics once SLOs exist; `dashboard-signal-audit` for surfacing them.

**Expected behavior**: identifies the critical user journey and what "good" means for it; chooses SLIs tied to that journey; proposes thresholds backed by historical traffic/latency data, or explicitly marks them as an unvalidated starting hypothesis; defines error-budget policy with a named owner and consequence.

**Bad behavior**: copying a round-number target (99.9%) from another service without justification; choosing an SLI that looks good but doesn't reflect the user journey; defining an error budget with no policy for exhaustion.

**Pass/fail**: PASS requires (a) SLIs traced to the user journey, (b) thresholds evidence-based or marked hypothesis, (c) owned error-budget policy. FAIL if a target is copied without justification.

---

## 52. Operational dashboard not useful during incidents

**User prompt**: "Review this operational dashboard. It has many graphs, but on-call says it is not useful during incidents."

**Expected skill**: `dashboard-signal-audit`
**Acceptable secondary**: `metrics-and-alerts-review` for alert threshold design; `slo-definition-review` for source-of-truth SLOs.

**Expected behavior**: inventories actual panels and classifies each as actionable or vanity based on real on-call decision-making; identifies missing panels tied to the reported gap; flags misleading visualizations (aggregation, time range); scores incident usefulness from real incident evidence, or marks it explicitly untested if no such evidence exists.

**Bad behavior**: assuming more panels means better coverage; giving a confident usefulness score with no incident evidence; generic "add more monitoring" advice.

**Pass/fail**: PASS requires (a) actionable-vs-vanity classification tied to real decisions, (b) usefulness score evidenced or marked untested, (c) specific recommended changes. FAIL if usefulness is asserted without evidence.

---

## 53. Slow query fast locally

**User prompt**: "This query is slow in production but fast locally. Review the query plan and recommend safe fixes."

**Expected skill**: `query-performance-review`
**Acceptable secondary**: `index-strategy-review` if index design across queries becomes central; `hot-path-profiling-review` if the bottleneck turns out to be outside the query itself.

**Expected behavior**: requests the production execution plan rather than trusting local timing; compares estimated vs actual rows; ties the bottleneck to specific plan nodes or missing indexes; notes write-cost tradeoffs for any index recommendation; includes a verification plan.

**Bad behavior**: concluding from local query timing alone; reading the plan's shape without estimated-vs-actual comparison; recommending an index with no cost tradeoff noted.

**Pass/fail**: PASS requires (a) production plan requested or used, (b) estimated-vs-actual comparison present, (c) write-cost noted for index recommendations. FAIL if local timing is treated as sufficient evidence.

---

## 54. Risky live-table migration

**User prompt**: "Review this migration that adds a non-null column and backfills a large live table."

**Expected skill**: `schema-migration-safety-review`
**Acceptable secondary**: `release-and-rollback-readiness` for general release process; `architecture-decision-record` for a broader schema-design debate.

**Expected behavior**: reads the actual migration diff; assesses lock/downtime risk against real table size and engine behavior instead of assuming it's safe; addresses the old/new app compatibility window; proposes batched/throttled backfill; verifies rollback is actually reversible rather than asserted.

**Bad behavior**: treating the migration as safe because "it's just adding a column"; ignoring the compatibility window between app versions; asserting a rollback plan without checking reversibility.

**Pass/fail**: PASS requires (a) lock/downtime risk tied to table size/engine, (b) compatibility window addressed, (c) rollback verified not assumed. FAIL if any is missing.

---

## 55. Index design vs query patterns

**User prompt**: "Review whether these proposed indexes match our most important query patterns."

**Expected skill**: `index-strategy-review`
**Acceptable secondary**: `query-performance-review` if a specific single query becomes the focus; `schema-migration-safety-review` for the creation migration's safety.

**Expected behavior**: ties each recommended index to a real query pattern; checks cardinality/selectivity rather than assuming it; states write/storage cost per index; rejects redundant or low-selectivity indexes with reasons; includes a verification step confirming planner usage.

**Bad behavior**: proposing an index for every filterable column; assuming cardinality without checking; ignoring write cost.

**Pass/fail**: PASS requires (a) each index tied to a query pattern, (b) at least one index rejected with a reason if applicable, (c) write cost stated. FAIL if indexes are recommended without query-pattern justification.

---

## 56. Duplicated invoices across services

**User prompt**: "Users are seeing duplicated invoices and inconsistent payment status across services."

**Expected skill**: `data-consistency-incident-diagnosis`
**Acceptable secondary**: `data-quality-contracts` if the issue turns out to be warehouse-side; `service-debugging` if it's a single-service runtime bug; `incident-triage-runbook` for coordination.

**Expected behavior**: confirms the declared source of truth rather than assuming it; traces the actual write/read/event path; separates known facts from suspected cause; keeps affected scope labeled confirmed vs estimated; proposes a scoped reconciliation plan, not a blind mass-update.

**Bad behavior**: correcting visible bad records without confirming full scope; asserting a root cause without tracing the path; assuming which store is authoritative.

**Pass/fail**: PASS requires (a) source of truth confirmed or requested, (b) known-vs-suspected separation, (c) no blind mass-correction proposed. FAIL if scope or cause is asserted without evidence.

---

## 57. Intermittent DB timeouts and deadlocks

**User prompt**: "Our app has intermittent DB timeouts and deadlocks under load."

**Expected skill**: `connection-pool-and-locking-debugging`
**Acceptable secondary**: `service-debugging` for general app bugs; `query-performance-review` if a specific query is the trigger; `incident-triage-runbook` for coordination.

**Expected behavior**: requests actual deadlock/lock-wait logs and pool configuration rather than defaulting to raising pool size or timeouts; checks transaction boundaries and connection lifecycle in code; ties the root cause to specific evidence; proposes a verification loop under representative load.

**Bad behavior**: recommending a bigger pool or longer timeout as the first response; diagnosing deadlocks without reading lock evidence; assuming local reproduction reflects production concurrency.

**Pass/fail**: PASS requires (a) lock/pool evidence requested or used, (b) root cause tied to evidence, (c) no default pool-size/timeout fix. FAIL if a resource-limit change is proposed without diagnosis.

---

## 58. Backup and restore readiness for production

**User prompt**: "Review whether our database backups and restore process are actually ready for production incidents."

**Expected skill**: `backup-restore-readiness`
**Acceptable secondary**: `runbook-writer` for documenting the restore procedure; `incident-triage-runbook` for incident-specific recovery coordination.

**Expected behavior**: treats backup-job success as distinct from restore success; requires evidence of a verified restore test before rating readiness; checks RPO/RTO against real test evidence, not the backup schedule alone; produces a concrete restore test plan with named ownership.

**Bad behavior**: rating the system ready because backups run successfully; asserting RPO/RTO is met without restore test evidence.

**Pass/fail**: PASS requires (a) restore test history requested or used, (b) risk rating reflects absence of a verified test if applicable, (c) a concrete restore test plan with an owner. FAIL if backup success alone is treated as readiness.

---

## 59. Slow endpoint with CPU profile and traces

**User prompt**: "This backend endpoint is slow. We have a CPU profile and request traces."

**Expected skill**: `hot-path-profiling-review`
**Acceptable secondary**: `trace-based-latency-diagnosis` if trace evidence becomes primary; `query-performance-review` if the bottleneck turns out to be a specific query.

**Expected behavior**: reads actual profile data (sample/time percentages) rather than eyeballing a flame graph; cross-checks hot functions against the trace to confirm they're on the critical path; ranks candidates by evidence strength; states rejected guesses; verifies with the same profiling method before/after.

**Bad behavior**: proposing optimizations from intuition without a profile; declaring success from average latency alone; optimizing a function not confirmed to be on the critical path.

**Pass/fail**: PASS requires (a) profile data used, not guessed, (b) critical-path confirmation, (c) same-method before/after verification plan. FAIL if optimization is proposed without profile evidence.

---

## 60. Worker memory grows until OOM-killed

**User prompt**: "Our worker memory grows over several hours until it gets OOM-killed."

**Expected skill**: `memory-leak-diagnosis`
**Acceptable secondary**: `kubernetes-workload-debugging` for pod-level OOM policy; `service-debugging` for general bugs; `caching-strategy-review` if an unbounded cache is the confirmed cause.

**Expected behavior**: requests memory metrics and heap/profile snapshots at different points in the growth window; traces suspected retention paths (caches, queues, timers) in code; states ruled-out causes explicitly; proposes a mitigation plan fixing the retention path, not a restart schedule; verifies over a realistic multi-hour timescale.

**Bad behavior**: recommending scheduled restarts as the fix; naming a retention path without a heap diff; verifying with a short synthetic test that doesn't match the leak's timescale.

**Pass/fail**: PASS requires (a) heap/profile evidence requested or used, (b) retention path tied to evidence, (c) no restart-as-fix recommendation. FAIL if a cause is named without heap-diff evidence.

---

## 61. Throughput ceiling despite adding workers

**User prompt**: "Our service cannot process more than 300 jobs/min even after adding workers."

**Expected skill**: `throughput-bottleneck-triage`
**Acceptable secondary**: `query-performance-review` if a DB query is the confirmed bottleneck; `metrics-and-alerts-review` for alert quality; `release-and-rollback-readiness` for a scaling rollout.

**Expected behavior**: requests utilization/queue-depth evidence at each layer rather than recommending further scaling; notes that throughput not improving with added workers suggests a shared downstream bottleneck; proposes safe, reversible experiments to confirm the hypothesis; ties scaling/optimization recommendations to the confirmed layer.

**Bad behavior**: recommending more workers/instances as the first response; declaring a bottleneck from a dashboard glance without utilization numbers; verifying under an unrealistic load shape.

**Pass/fail**: PASS requires (a) utilization/queue evidence requested or used, (b) hypothesis tied to a specific layer, (c) no further-scaling recommendation before confirmation. FAIL if scaling is proposed without diagnosis.

---

## 62. Caching decision with staleness concern

**User prompt**: "Should we add caching to speed up this endpoint? We are worried about stale data."

**Expected skill**: `caching-strategy-review`
**Acceptable secondary**: `data-consistency-incident-diagnosis` if inconsistency is already occurring; `hot-path-profiling-review` if profiling should precede the caching decision; `architecture-review` for broader tradeoffs.

**Expected behavior**: requests read/write ratio and acceptable staleness window rather than treating caching as a default win; addresses stampede risk and fallback behavior explicitly; proposes a concrete invalidation plan tied to the confirmed freshness requirement.

**Bad behavior**: recommending caching as a default performance fix; no plan for stampede risk; assuming the fallback path is safe without checking it.

**Pass/fail**: PASS requires (a) read/write ratio and staleness requested or used, (b) stampede and fallback risk addressed, (c) concrete invalidation plan. FAIL if caching is recommended without freshness/access-pattern evidence.

---

## 63. Load test review before launch

**User prompt**: "Review these load test results and tell us whether we are ready to launch."

**Expected skill**: `load-test-result-review`
**Acceptable secondary**: `release-and-rollback-readiness` for the broader release process; `throughput-bottleneck-triage` if the ceiling itself needs diagnosis; `slo-definition-review` for target definition.

**Expected behavior**: requests latency percentiles and error rates, not just averages; assesses workload realism and environment parity against real production traffic; identifies the saturation point from evidence; withholds a "go" recommendation until percentile/parity gaps are resolved; proposes concrete follow-up tests.

**Bad behavior**: accepting an average-latency-only report as sufficient; issuing a go recommendation without percentile or parity evidence; ignoring ramp-pattern mismatch with real traffic.

**Pass/fail**: PASS requires (a) percentile/error-rate evidence requested or used, (b) realism/parity assessed, (c) go/no-go reasoning stated, not just asserted. FAIL if a go recommendation is issued without percentile/parity evidence.

---

## Scoring across the suite

- **Routing score**: 63/63 scenarios should load the expected skill; scenarios 2/3, 4/5/6, 16/17, 18/19/20/21/22/23/24, 25/26/27/28/29/30, 31/32/33/34/35/36, 37/38/39/40/41/42, 43/44/45/46/47, 48/49/50/51/52, and 53/54/55/56/57/58/59/60/61/62/63 also test that near-neighbor skills *don't* fire or hand off explicitly.
- **Behavior-delta score**: for each scenario, ask "which of these actions would a capable agent have skipped without the skill?" A run can be pleasant and still FAIL if the skill-specific behaviors (persistence, evidence requests, double-run proofs, backtests) are absent.
- **Reference wiring**: in at least a spot-check of runs, confirm the agent actually loaded `references/checklist.md` before its handoff — that wiring is what makes the reference files real.
