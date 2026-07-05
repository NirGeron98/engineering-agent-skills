# Sample task — Accessibility Review

## Prompt

"Audit the new account switcher dropdown for accessibility before release."

## Abbreviated run

1. Opened dropdown component, trigger, tests, and story.
2. Ran available story and inspected DOM.
3. Keyboard check found Tab moves behind the open menu and Escape does not close.
4. Axe passed; manual check still found focus defects.
5. Loaded `references/checklist.md` before handoff.

## Expected output

```markdown
## Accessibility review: account switcher dropdown

**Findings**
- High — focus escapes behind the open menu — impact: keyboard users can activate hidden page controls — evidence: manual Tab check in Storybook — fix: use menu/listbox pattern with managed focus or close on blur.
- High — trigger lacks expanded state — impact: screen-reader users do not know the menu opened — evidence: rendered button has no `aria-expanded` — fix: set `aria-expanded` and `aria-controls`.
- Medium — Escape does not close — evidence: keyboard check — fix: handle Escape and restore focus to trigger.
```
