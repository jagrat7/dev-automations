# oxc-setup

Set up oxlint + oxfmt for a JS/TS project — replaces ESLint + Prettier with one fast Rust toolchain.

## What this skill does

- detects package manager, framework, and existing style
- installs `oxlint`, `oxfmt`, `oxlint-tsgolint`
- writes `.oxlintrc.json` + `.oxfmtrc.json` adapted to the project
- adds `lint`, `lint:fix`, `fmt`, `fmt:check` scripts
- configures VS Code (`oxc.oxc-vscode`)
- formats + fixes the codebase for a clean baseline

## Install with `npx skills`

```bash
npx skills add jagrat7/dev-automations --skill oxc-setup
```

## Use the skill

```text
Use oxc-setup to add oxlint and oxfmt to this project.
```

## Full Instructions

- `skills/oxc-setup/SKILL.md`
