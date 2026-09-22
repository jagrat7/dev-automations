---
name: oxc-setup
description: "Set up oxlint (linter) and oxfmt (formatter) for a TypeScript project"
disable-model-invocation: true
---

# Oxc Setup

## Overview

Set up [Oxc](https://oxc.rs) — oxlint (linter) + oxfmt (formatter) — for a JS/TS project. 

Adapt the setup to the project's package manager, existing style, framework, architecture, and generated files.


### Step 1: Detect Project Context

Read these to adapt the setup:

- `package.json` — package manager (bun/pnpm/npm/yarn via lockfile), existing lint/format scripts, dependencies (React? Vue? TanStack?)
- existing config files (`.eslintrc*`, `eslint.config.*`, `.prettierrc*`, `prettier.config.*`) — note style choices to preserve
- `tsconfig.json` — path aliases (e.g. `#/*`, `@/*`)
- `.gitignore` — build output dirs, generated files
- a sample source file — detect semicolon usage and quote style
- For a full-stack app, inspect its env/config boundary, server module entry points, and service file conventions before considering architecture rules

### Step 2: Install Packages

Use the project's package manager (detect from lockfile: `bun.lock` → bun, `pnpm-lock.yaml` → pnpm, `package-lock.json` → npm, `yarn.lock` → yarn):

```bash
bun add -D oxlint oxfmt oxlint-tsgolint
```


### Step 3: Write `.oxlintrc.json`

Write a config adapted to the project. Key decisions:

- **plugins**: always `typescript`, `unicorn`, `oxc`. Add `react` if React detected. Add `jsdoc` only if asked.
- **categories**: `correctness: "error"`, `suspicious: "warn"`. Add `perf: "warn"` only if asked.
- **ignorePatterns**: `node_modules`, build dirs from `.gitignore` + `package.json`, generated files (e.g. `routeTree.gen.ts`, `**/*.gen.ts`), agent/tool dirs (`.agents`, `.claude`, `.codex`, `.output`, `.tanstack`, `.nitro`, `.venv`)
- **rules**: keep minimal. Only add what the project's global conventions require (e.g. `unicorn/filename-case` kebab if the project uses kebab-case files).

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
  "semi": false,
  "sortTailwindcss": true,
  "sortPackageJson": true,
  "ignorePatterns": [
    "node_modules/**",
    "dist/**",
    "**/*.gen.ts",
    "<lockfile>"
  ]
}
```

If Tailwind is detected (in deps or `tailwind.config.*`), set `sortTailwindcss: true` and add the `functions` list for any class-merge utilities the project uses (`clsx`, `cn`, `cva`).

### Step 5: Add `package.json` Scripts

Add these to `scripts` (preserve existing scripts, don't overwrite):

```json
"lint": "oxlint .",
"lint:fix": "oxlint --fix .",
"fmt": "oxfmt .",
"fmt:check": "oxfmt --check ."
```


## Custom Rules (optional)

For a full-stack app, read the [oxlint rule examples](references/fullstack-oxlint.md). Add a rule only when the project already follows that convention. Check its code before choosing paths, aliases, options, and file scope.

## Common Mistakes

- **Hardcoding ignore patterns** — always derive from the project's `.gitignore` and build setup
- **Enabling too many plugins** — stick to typescript/unicorn/oxc + framework plugin. Skip jsx-a11y/import unless explicitly asked; they're noisy
- **Over-tuning rules upfront** — start minimal, add rules only when a real issue surfaces
- **Not formatting after lint:fix** — lint fixes can change formatting; always run `fmt` last
- **Ignoring generated files** — `routeTree.gen.ts`, `*.gen.ts`, build output must be in `ignorePatterns` or they'll spam warnings
