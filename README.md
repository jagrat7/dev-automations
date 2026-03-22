# dev-automations

A small collection of reusable agent skills.

## Available Skills

- `frontend-variants` - builds 3 distinct frontend UI variants using isolated worktrees, shadcn presets, and design specs

## Usage (with `npx skills`)

Use the Skills CLI to discover and install skills from this repo.

```bash
# Explore available commands
npx skills --help

# Search for skills
npx skills search frontend

# Install this skill from this repository
npx skills add jagrat7/dev-automations --skill frontend-variants

# List installed skills
npx skills list
```

After installing, use your agent normally and call the skill by name in your prompt.

Example prompt:

```text
Use frontend-variants to create 3 design variants for this app.
```

## Repo Structure

- `skills/frontend-variants/SKILL.md` - full skill instructions
- `skills/frontend-variants/README.md` - quick usage guide for this skill
