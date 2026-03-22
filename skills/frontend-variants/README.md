# frontend-variants

Create 3 visually distinct frontend-only variants for an existing app using isolated git worktrees and different shadcn presets.

## What this skill does

- creates 3 separate worktrees for safe theme isolation
- chooses distinct style directions
- maps each direction to a design spec
- applies a matching shadcn preset per variant
- builds a realistic frontend shell (no backend wiring)

## Install with `npx skills`

```bash
# From anywhere
npx skills add jagrat7/dev-automations --skill frontend-variants
```

## Use the skill

After install, invoke it in your agent prompt:

```text
Use frontend-variants to generate 3 complete UI shell variants for this project.
```

You can also be specific:

```text
Use frontend-variants for this SaaS dashboard and prioritize premium, playful, and minimalist styles.
```

## Full Instructions

For complete implementation steps, see:

- `skills/frontend-variants/SKILL.md`
