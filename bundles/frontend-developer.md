# Frontend Developer Bundle

Optional role-based recommendations for engineers building user interfaces across React, Vue, Angular, Svelte, or plain TypeScript: components, state, forms, accessibility, performance, RTL, and API integration.

For task-based discovery, start from [../SKILL_CATALOG.md](../SKILL_CATALOG.md).

## Minimum Start

Start with shared workflow skills, then add one frontend skill that matches the work in front of you.

- [.claude/skills/repo-onboarding](../.claude/skills/repo-onboarding/SKILL.md) - map the app structure, scripts, and conventions before changing UI code.
- [.claude/skills/diagnose-bug](../.claude/skills/diagnose-bug/SKILL.md) - fix a narrowed, reproducible UI bug from evidence.
- [.claude/skills/code-review](../.claude/skills/code-review/SKILL.md) - review implementation risk before merge.
- One of:
  - [.claude/skills/component-design-review](../.claude/skills/component-design-review/SKILL.md) - when the main work is a reusable component.
  - [.claude/skills/api-integration-debugging](../.claude/skills/api-integration-debugging/SKILL.md) - when the main work is a frontend/backend integration bug.

## Recommended Set

After the minimum start feels useful, add these based on the kind of frontend work you do most often.

- [.claude/skills/component-design-review](../.claude/skills/component-design-review/SKILL.md) - review reusable component boundaries, public API, state ownership, styling hooks, accessibility hooks, and testability.
- [.claude/skills/frontend-state-management](../.claude/skills/frontend-state-management/SKILL.md) - untangle local, URL, global, server/cache, form, optimistic, and derived state.
- [.claude/skills/form-validation-review](../.claude/skills/form-validation-review/SKILL.md) - review validation rules, client/server boundaries, async checks, error mapping, submission UX, accessibility, and reset behavior.
- [.claude/skills/accessibility-review](../.claude/skills/accessibility-review/SKILL.md) - audit keyboard, focus, semantics, labels, ARIA, announcements, contrast, reduced motion, and screen-reader behavior.
- [.claude/skills/api-integration-debugging](../.claude/skills/api-integration-debugging/SKILL.md) - debug request/response shape, auth, CORS, loading/error/empty states, retries, stale cache, and env vars at the frontend/backend boundary.

## Situational Skills

- [.claude/skills/rtl-ui-review](../.claude/skills/rtl-ui-review/SKILL.md) - use when the UI supports RTL locales or mixed LTR/RTL content.
- [.claude/skills/frontend-performance-review](../.claude/skills/frontend-performance-review/SKILL.md) - use when a route is slow, janky, repeatedly fetching, hydration-heavy, or bundle-sensitive.

## Related Skills

- [.claude/skills/api-design-review](../.claude/skills/api-design-review/SKILL.md) when the API contract itself, not the integration code, needs review.
- [.claude/skills/service-debugging](../.claude/skills/service-debugging/SKILL.md) when browser evidence proves a deployed service is the failing side.
- [.claude/skills/deployment-debugging](../.claude/skills/deployment-debugging/SKILL.md) when the frontend symptom depends on deployed config, artifact version, routing, or runtime environment.
