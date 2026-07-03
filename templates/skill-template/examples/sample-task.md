# Sample task: <skill-name>

This is a worked example for evaluators and skill authors — it is not loaded at runtime.

## User prompt

> "<a realistic, specific request that should trigger this skill's frontmatter description>"

## Abbreviated run

1. The agent reads the frontmatter description, confirms this is the right skill (not an adjacent one), and follows the workflow steps in `SKILL.md`.
2. It gathers the evidence the workflow requires (files, commands, logs, or other artifacts) rather than assuming or guessing.
3. It reaches a point where it needs evidence it cannot obtain itself.

## Obstacle / missing evidence moment

> "<describe the point where the agent lacks direct access — e.g., no shell access to a remote system, no query runner, no browser>"

## Exact evidence request

The agent asks for the precise artifact instead of proceeding on a guess, for example:

```
Run: <exact command>
Paste back: <exact output needed>
```

Any conclusion that depends on this evidence is marked pending until it arrives.

## Expected output

```markdown
<the skill's Expected output format, filled in with this example's findings>
```

## Why this passes the quality bar

- Every claim in the output cites the evidence gathered in the run, not assumption.
- The obstacle was handled by requesting exact evidence, not by fabricating a plausible-sounding answer.
- The output matches the skill's declared output format and would let a reviewer or future agent act on it without re-investigating.
