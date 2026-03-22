---
name: frontend-variants
description: Use when building a frontend with 3 distinct UI style variants using shadcn presets, worktrees, and ui-ux-pro-max design systems — each variant gets its own isolated git worktree with a unique shadcn preset skeleton and theme.
---

# Frontend Variants

## Overview

Build 3 fully isolated frontend-only UI shells for an existing app — each in its own git worktree, each bootstrapped with a distinct shadcn preset. The goal is a themed, navigable shell with realistic placeholder content — **no backend wiring, no API calls, no auth logic**.

Read the project's `README.md` and any description files first to understand what the app does, its pages, and its data model. Use that context to populate realistic placeholder content in the shell.

**Why worktrees:** `shadcn init --preset <code>` rewrites `globals.css`, `tailwind.config`, and `components.json`. Running all 3 in the same directory would overwrite each other. Worktrees give each variant a fully isolated filesystem.

**REQUIRED SKILLS:**
- `ui-ux-pro-max` — design system generation, style selection, color/typography/component specs
- `shadcn` — preset browsing, component installation, theme configuration

## Workflow

### Step 0: Read App Context & Create `.design/` Folder

Before picking styles or writing any code, read:

- `README.md` — app purpose, features, target users
- Any description/spec files in the repo

Then create a `.design/` folder at the project root — this is where all specs live:

```bash
mkdir -p .design
```

Extract and keep in mind: app name, purpose, target user, key pages, data entities, and tone. Use this to drive style selection and realistic placeholder content — no need to save to file.

---

### Step 1: Pick 3 Visually Distinct Styles (ui-ux-pro-max)

Use `ui-ux-pro-max` to recommend 3 styles based on the app context extracted in Step 0:

```bash
python3 ~/.claude/skills/ui-ux-pro-max/scripts/search.py "<app_purpose> <tone> <target_user>" --domain style -n 10
```

From the results, pick 3 that are visually distinct — different moods, different visual languages. Avoid picking styles that are too similar (e.g. don't pick both Glassmorphism and Liquid Glass).

Document the 3 chosen styles and save to `.design/variants.json` in the project root:

```json
{
  "variant_1": { "style": "", "why": "", "worktree": "../<project>-variant-1" },
  "variant_2": { "style": "", "why": "", "worktree": "../<project>-variant-2" },
  "variant_3": { "style": "", "why": "", "worktree": "../<project>-variant-3" }
}
```

Then **immediately create all 3 worktrees** — all subsequent steps happen inside them:

```bash
# From the project root
git worktree add ../project-variant-1 -b variant/style1
git worktree add ../project-variant-2 -b variant/style2
git worktree add ../project-variant-3 -b variant/style3
```

---

### Step 2: Generate Design System per Variant (ui-ux-pro-max)

Navigate into each worktree, run the design system generator, then save the filled spec as `.design/variant.json` **inside that worktree**. All color values must use **OKLCH format** (`oklch(lightness chroma hue)`) — never hex:

```bash
cd ../project-variant-1
python3 ~/.claude/skills/ui-ux-pro-max/scripts/search.py "<style1 keywords>" --design-system -p "Variant 1"
# fill .design/variant.json, then repeat for variant-2 and variant-3
```

Fill out this spec:

```json
{
  "overview": {
    "style_name": "",
    "creative_north_star": "",
    "description": "",
    "best_for": [],
    "worktree": ""
  },
  "colors": {
    "palette": {
      "primary": "",
      "primary_container": "",
      "secondary": "",
      "tertiary": "",
      "cta": "",
      "background": "",
      "text": "",
      "notes": ""
    },
    "surface_hierarchy": {
      "surface": "",
      "surface_container_low": "",
      "surface_container": "",
      "surface_container_high": "",
      "surface_container_highest": "",
      "surface_bright": ""
    },
    "rules": [],
    "signature_effect": ""
  },
  "typography": {
    "display":  { "font": "", "usage": "" },
    "body":     { "font": "", "usage": "" },
    "mono":     { "font": "", "usage": "" },
    "css_import": ""
  },
  "elevation": {
    "approach": "",
    "ambient_shadow": "",
    "ghost_border": "",
    "glassmorphism": ""
  },
  "components": {
    "buttons": { "primary": "", "hover": "", "tertiary": "" },
    "cards":   { "surface": "", "separator": "", "active_state": "" },
    "inputs":  { "shape": "", "focus": "", "labels": "" },
    "chips":   { "style": "" }
  },
  "layout": {
    "pattern": "",
    "sections": [],
    "cta_placement": "",
    "philosophy": ""
  },
  "dos": [],
  "donts": []
}
```

---

### Step 3: Pick Shadcn Preset per Variant (shadcn skill)

Browse presets at [ui.shadcn.com/create](https://ui.shadcn.com/create). Use the `shadcn` skill to search for presets matching each variant's style. Fill out and save as `.design/preset.json` inside each worktree before running init:

```json
{
  "preset_code": "",
  "preset_name": "",
  "preset_url": "",
  "why_this_preset": "",
  "base": "",
  "style": "",
  "icon_library": "",
  "extra_icon_package": "",
  "components_to_install": [],
  "overrides_needed": {
    "primary": "oklch(...)",
    "secondary": "oklch(...)",
    "accent": "oklch(...)",
    "background": "oklch(...)",
    "foreground": "oklch(...)",
    "radius": "",
    "font_sans": "",
    "font_mono": ""
  }
}
```

> - `base`: `radix` or `base` — affects all component APIs (`asChild` vs `render`)
> - `style`: visual treatment from the preset (e.g. `nova`, `vega`)
> - `icon_library`: `lucide` or `tabler` — the shadcn-managed icon set in `components.json`
> - `extra_icon_package`: optional decorative icons outside shadcn (e.g. `@phosphor-icons/react`)
> - All color overrides in **OKLCH** format — never hex

Then init in each worktree:

```bash
cd ../project-variant-1 && bunx shadcn init --preset <preset-code-1>
cd ../project-variant-2 && bunx shadcn init --preset <preset-code-2>
cd ../project-variant-3 && bunx shadcn init --preset <preset-code-3>
```

The preset seeds `globals.css` and `tailwind.config` with the theme's color tokens. **Always override with the exact OKLCH values from your design spec** — presets are starting points, not final themes.

---

### Step 4: Map Design Spec → CSS Variables

In each worktree's `globals.css`, map the full design spec:

```css
:root {
  /* Core */
  --background:          <surface>;
  --foreground:          <text>;
  --primary:             <primary>;
  --primary-foreground:  <contrast text>;
  --secondary:           <secondary>;
  --secondary-foreground:<contrast text>;
  --accent:              <tertiary>;
  --accent-foreground:   <contrast text>;

  /* Surface hierarchy */
  --card:                <surface_container>;
  --card-foreground:     <text>;
  --muted:               <surface_container_low>;
  --muted-foreground:    <text at 60% opacity>;
  --popover:             <surface_container_high>;
  --popover-foreground:  <text>;

  /* Borders & interaction */
  --border:              <ghost_border>;
  --input:               <surface_container_low>;
  --ring:                <primary>;

  /* Radius */
  --radius:              <base_radius from preset spec>;
}
```

Also add Google Fonts import from the typography spec at the top of `globals.css`:

```css
@import url('<css_import from typography spec>');
```

And set font variables in `tailwind.config`:

```js
fontFamily: {
  sans: ['<body font>', 'sans-serif'],
  display: ['<display font>', 'sans-serif'],
  mono: ['<mono font>', 'monospace'],
}
```

---

### Step 5: Build the Shell (max 3 pages)

Build a navigable frontend-only shell — **no API calls, no auth, no backend wiring**. Use realistic placeholder content derived from the app context in Step 0.

**Pages to build (pick up to 3 from `key_pages`):**

```json
{
  "page_1": { "name": "", "route": "", "purpose": "", "key_components": [] },
  "page_2": { "name": "", "route": "", "purpose": "", "key_components": [] },
  "page_3": { "name": "", "route": "", "purpose": "", "key_components": [] }
}
```

**Shell requirements:**

- Navbar or sidebar with navigation between the 3 pages
- Each page uses the variant's shadcn components, colors, and typography from the design spec
- Placeholder data that matches the app's domain (no "Lorem ipsum" — use real-looking content)
- Responsive layout (mobile + desktop)
- Apply `dos` and `donts` from the design spec throughout

**What to skip:**

- Form submissions (show the form, don't wire it)
- Data fetching (hardcode realistic mock data inline)
- Authentication flows (show logged-in state by default)
- Error/loading states (show happy path only)

---

## Common Mistakes

- **Same preset for all 3** — defeats the purpose; each must produce a visually distinct skeleton
- **Skipping the design spec** — results in ad-hoc color drift that doesn't match the intended style
- **Not overriding preset colors** — presets are starting points; always map exact OKLCH values from spec
- **Running `shadcn init` without `--preset`** — generates a generic skeleton with no theme character
- **Installing all components upfront** — use `bunx shadcn add` per component to avoid bloat
- **Working in the same directory for all 3** — shadcn rewrites config files; always use separate worktrees
