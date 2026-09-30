# dev-automations

A small collection of reusable agent skills.

## Available Skills

- `comb` - splits changes into focused commits and pushes; add `pr` to open or reuse a pull request
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

# Install the commit-and-push skill
npx skills add jagrat7/dev-automations --skill comb

# List installed skills
npx skills list
```

After installing, use your agent normally and call the skill by name in your prompt.

Example prompt:

```text
Use frontend-variants to create 3 design variants for this app.
```

To organize changes into commits and push, use `$comb`. Use
`$comb pr` to also open or reuse a pull request.

## Repo Structure

- `skills/comb/SKILL.md` - commit grouping, push, and optional PR workflow
- `skills/frontend-variants/SKILL.md` - full skill instructions
- `skills/frontend-variants/README.md` - quick usage guide for this skill
