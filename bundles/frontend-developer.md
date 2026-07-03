# Frontend Developer Bundle

Optional role-based recommendations for engineers building user interfaces across React, Vue, Angular, Svelte, or plain TypeScript: components, state, forms, accessibility, performance, RTL, and API integration.

For task-based discovery, start from [../SKILL_CATALOG.md](../SKILL_CATALOG.md).

## Minimum Start

Start with shared workflow skills, then add one frontend skill that matches the work in front of you.

- [skills/planning/repo-onboarding](../skills/planning/repo-onboarding/SKILL.md) - map the app structure, scripts, and conventions before changing UI code.
- [skills/debugging/diagnose-bug](../skills/debugging/diagnose-bug/SKILL.md) - fix a narrowed, reproducible UI bug from evidence.
- [skills/review/code-review](../skills/review/code-review/SKILL.md) - review implementation risk before merge.
- One of:
  - [skills/frontend/component-design-review](../skills/frontend/component-design-review/SKILL.md) - when the main work is a reusable component.
  - [skills/frontend/api-integration-debugging](../skills/frontend/api-integration-debugging/SKILL.md) - when the main work is a frontend/backend integration bug.

## Recommended Set

After the minimum start feels useful, add these based on the kind of frontend work you do most often.

- [skills/frontend/component-design-review](../skills/frontend/component-design-review/SKILL.md) - review reusable component boundaries, public API, state ownership, styling hooks, accessibility hooks, and testability.
- [skills/frontend/frontend-state-management](../skills/frontend/frontend-state-management/SKILL.md) - untangle local, URL, global, server/cache, form, optimistic, and derived state.
- [skills/frontend/form-validation-review](../skills/frontend/form-validation-review/SKILL.md) - review validation rules, client/server boundaries, async checks, error mapping, submission UX, accessibility, and reset behavior.
- [skills/frontend/accessibility-review](../skills/frontend/accessibility-review/SKILL.md) - audit keyboard, focus, semantics, labels, ARIA, announcements, contrast, reduced motion, and screen-reader behavior.
- [skills/frontend/api-integration-debugging](../skills/frontend/api-integration-debugging/SKILL.md) - debug request/response shape, auth, CORS, loading/error/empty states, retries, stale cache, and env vars at the frontend/backend boundary.

## Situational Skills

- [skills/frontend/rtl-ui-review](../skills/frontend/rtl-ui-review/SKILL.md) - use when the UI supports RTL locales or mixed LTR/RTL content.
- [skills/frontend/frontend-performance-review](../skills/frontend/frontend-performance-review/SKILL.md) - use when a route is slow, janky, repeatedly fetching, hydration-heavy, or bundle-sensitive.

## Related Skills

- [skills/api-and-backend/api-design-review](../skills/api-and-backend/api-design-review/SKILL.md) when the API contract itself, not the integration code, needs review.
- [skills/api-and-backend/service-debugging](../skills/api-and-backend/service-debugging/SKILL.md) when browser evidence proves a deployed service is the failing side.
- [skills/devops-infrastructure/deployment-debugging](../skills/devops-infrastructure/deployment-debugging/SKILL.md) when the frontend symptom depends on deployed config, artifact version, routing, or runtime environment.
