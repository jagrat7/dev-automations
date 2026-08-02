---
name: oxc-setup
description: "Set up oxlint (linter) and oxfmt (formatter) for a JavaScript/TypeScript project - installs packages, writes config files, adds scripts, and configures VS Code. Adapts to the project's existing style and stack."
---

# Oxc Setup

## Overview

Set up [Oxc](https://oxc.rs) — oxlint (linter) + oxfmt (formatter) — for a JS/TS project. Fast Rust-based linting and formatting, minimal config.

This skill adapts to the project: detects package manager, existing style (semicolons, quotes), framework (React/Vue/plain), and generated files to ignore.

## Workflow

### Step 1: Detect Project Context

Read these to adapt the setup:

- `package.json` — package manager (bun/pnpm/npm/yarn via lockfile), existing lint/format scripts, dependencies (React? Vue? TanStack?)
- existing config files (`.eslintrc*`, `eslint.config.*`, `.prettierrc*`, `prettier.config.*`) — note style choices to preserve
- `tsconfig.json` — path aliases (e.g. `#/*`, `@/*`)
- `.gitignore` — build output dirs, generated files
- a sample source file — detect semicolon usage

### Step 2: Install Packages

Use the project's package manager (detect from lockfile: `bun.lock` → bun, `pnpm-lock.yaml` → pnpm, `package-lock.json` → npm, `yarn.lock` → yarn):

```bash
# bun
bun add -D oxlint oxfmt oxlint-tsgolint

# pnpm
pnpm add -D oxlint oxfmt oxlint-tsgolint

# npm
npm add -D oxlint oxfmt oxlint-tsgolint
```

`oxlint-tsgolint` enables type-aware linting (catches real bugs like unawaited promises and `await` on non-promises).

If the project already has ESLint/Prettier configs and the user wants to switch, remove them after confirming — otherwise leave them alone.

### Step 3: Write `.oxlintrc.json`

Write a config adapted to the project. Key decisions:

- **plugins**: always `typescript`, `unicorn`, `oxc`. Add `react` if React detected. Add `jsdoc` only if asked.
- **categories**: `correctness: "error"`, `suspicious: "warn"`. Add `perf: "warn"` only if asked.
- **ignorePatterns**: `node_modules`, build dirs from `.gitignore` + `package.json`, generated files (e.g. `routeTree.gen.ts`, `**/*.gen.ts`), agent/tool dirs (`.agents`, `.claude`, `.codex`, `.output`, `.tanstack`, `.nitro`, `.venv`)
- **rules**: keep minimal — only the project's custom `local/*` rules plus conventions it actually uses (e.g. `unicorn/filename-case` kebab if the project uses kebab-case files). Do not add stock rule presets like `no-unused-vars` configs.
- **type-aware**: do not set `options.typeAware` in the config file; enable it in VS Code settings instead (Step 6).

Base template (adapt plugins/ignores per Step 1 findings):

```json
{
  "$schema": "./node_modules/oxlint/configuration_schema.json",
  "plugins": ["typescript", "unicorn", "oxc"],
  "categories": {
    "correctness": "error",
    "suspicious": "warn"
  },
  "ignorePatterns": [
    "node_modules/**",
    "dist/**",
    "**/*.gen.ts"
  ],
  "rules": {}
}
```

If React is detected, add `"react"` to `plugins`.

If the project uses kebab-case filenames, add:

```json
"unicorn/filename-case": [
  "error",
  { "case": "kebabCase", "ignore": ["<generated-file-patterns>"] }
]
```

### Step 4: Write `.oxfmtrc.json`

Adapt to the project's existing style detected in Step 1:

```json
{
  "$schema": "./node_modules/oxfmt/configuration_schema.json",
  "semi": <false if no semicolons detected, else true>,
  "sortTailwindcss": <true if tailwindcss detected>,
  "sortPackageJson": true,
  "ignorePatterns": [
    "node_modules/**",
    "dist/**",
    "**/*.gen.ts",
    "<lockfile>"
  ]
}
```

If Tailwind is detected (in deps or `tailwind.config.*`), set `sortTailwindcss: true`.

### Step 5: Add `package.json` Scripts

Add these to `scripts` (preserve existing scripts, don't overwrite):

```json
"lint": "oxlint .",
"lint:fix": "oxlint --fix .",
"fmt": "oxfmt .",
"fmt:check": "oxfmt --check ."
```

### Step 6: Configure VS Code

If `.vscode/` exists (or the user wants editor setup), merge into `.vscode/settings.json`:

```json
{
  "editor.defaultFormatter": "oxc.oxc-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.oxc": "always"
  },
  "[javascript]": { "editor.defaultFormatter": "oxc.oxc-vscode" },
  "[javascriptreact]": { "editor.defaultFormatter": "oxc.oxc-vscode" },
  "[typescript]": { "editor.defaultFormatter": "oxc.oxc-vscode" },
  "[typescriptreact]": { "editor.defaultFormatter": "oxc.oxc-vscode" },
  "[json]": { "editor.defaultFormatter": "oxc.oxc-vscode" },
  "[jsonc]": { "editor.defaultFormatter": "oxc.oxc-vscode" }
}
```

Write `.vscode/extensions.json`:

```json
{
  "recommendations": ["oxc.oxc-vscode"]
}
```

If `oxlint-tsgolint` is installed, add `"oxc.typeAware": true` to settings.

### Step 7: Format + Fix

Run once to establish a clean baseline:

```bash
bun run fmt
bun run lint:fix
```

If lint reports errors after fix, either:
- fix the remaining issues in code, OR
- adjust the config (turn off noisy rules with `off`, or downgrade to `warn`)

Target: `lint` exits 0 (warnings are fine). Re-run `fmt` after any lint fixes since fixes can change formatting.

### Step 8: Document in AGENTS.md

If an `AGENTS.md` exists, append a short section:

```markdown
## Lint & format

- `bun run lint` / `bun run lint:fix` — oxlint
- `bun run fmt` / `bun run fmt:check` — oxfmt
- Config: `.oxlintrc.json`, `.oxfmtrc.json`
```

## Custom Rules (optional)

If the project has custom oxlint JS plugins (e.g. in `dev/oxlint/`), wire them via `jsPlugins`:

```json
"jsPlugins": [
  { "name": "local", "specifier": "./dev/oxlint/index.mjs" }
]
```

Then enable rules under `local/<rule-name>`. Use `overrides` to scope rules to specific file patterns.

## Common Mistakes

- **Hardcoding ignore patterns** — always derive from the project's `.gitignore` and build setup
- **Enabling too many plugins** — stick to typescript/unicorn/oxc + framework plugin. Skip jsx-a11y/import unless explicitly asked; they're noisy
- **Over-tuning rules upfront** — start minimal, add rules only when a real issue surfaces
- **Not formatting after lint:fix** — lint fixes can change formatting; always run `fmt` last
- **Ignoring generated files** — `routeTree.gen.ts`, `*.gen.ts`, build output must be in `ignorePatterns` or they'll spam warnings
