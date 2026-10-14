---
name: icons
description: Find brand logos and icons as SVG. Use when a task needs a company, product, or tech logo or icon.
---

# Icons

Check these sources for brand logos in order. Stop at the first one with a good match.


0. **[svgl](https://svgl.app).** Search with `https://api.svgl.app?search={name}`. Each result has `route` and sometimes `wordmark`. Each is either a URL string or `{light, dark}` URLs. `brandUrl` links to the brand page when one exists.
0. **[theSVG](https://thesvg.org).** Fetch `https://thesvg.org/api/registry.json` once and filter `.icons[]` by `slug`, `title`, or `aliases`. Download `https://thesvg.org/icons/{slug}/{variant}.svg`. Variants are listed per icon: `default`, `mono`, `light`, `dark`, `wordmark`, `wordmarkLight`, `wordmarkDark`.
0. **Brand assets page.** Look for the brand's official press kit or brand page, such as `/brand`, `/press`, `/media-kit`, or a "Brand assets" link in the footer. Use its SVGs and follow its usage rules.
0. **Scrape the site.** Look for an inline `<svg>` logo in the header, `<link rel="icon">` or `mask-icon` SVGs, and `/favicon.svg`. If no SVG exists, take the largest raster image (`apple-touch-icon`, `og:image`, logo PNG) and convert it to SVG.

## Converting raster to SVG

- Trace with installed tools first: `vtracer` for color logos, or `magick` + `potrace -s` for single-color ones. With only `potrace`, trace each color separately. Never wrap the PNG in an `<svg>`.
- If no tool fits, ask before installing and offer options: install `vtracer`, keep the raster, or stop.
- Remove any background that isn't part of the logo, but keep transparency.
- Compare with the original. Simplify noisy paths and fix colors to match the brand.
- Say the icon was converted.

## Output

- Pick light and dark variants to match the target background. Use the one-color `mono` version when the icon should match the surrounding text color.
