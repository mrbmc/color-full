# color-full

A single-file color translator web app — no build step, no dependencies, no framework.

## Project structure

- `index.html` — the entire app (HTML + CSS + JS in one file)
- `manifest.json` — PWA manifest (standalone mode hides the header)

## What it does

Converts colors between formats in real time:
- **HEX** (`#rrggbb` / `#rrggbbaa`)
- **RGBA** (0–255 / 0–1 alpha)
- **HSLA** (hue 0–360°, saturation/lightness 0–100%)
- **HSBA / HSV** (hue 0–360°, saturation/brightness 0–100%)

Each format section has text inputs, sliders, and a copy button. Clicking the format label also copies. Sections are collapsible (state saved to `localStorage`).

A color chip at the top shows the active color and opens the native OS color picker on click. The chip also displays the hex value and an RGBA/HSL summary.

A **Palette** section stores up to 8 colors in `localStorage`. Click a swatch to load it; right-click for a context menu with Load / Save / Delete options.

## Architecture

- **Single source of truth**: `state = { r, g, b, a }` (RGBA floats)
- **Render loop**: `render(source)` updates all fields from state, skipping the source section to avoid cursor jumps. `updating` flag prevents re-entrant renders.
- **Color math**: pure JS functions — `rgbToHsl`, `hslToRgb`, `rgbToHsb`, `hsbToRgb`, `rgbaToHex`, `hexToRgba`
- **Slider backgrounds**: `updateSliderBgs()` sets gradient backgrounds on each range input to visualize the color channel it controls
- **Collapsible sections**: `toggleSection(id)` / `applyCollapsed()` — persisted in `localStorage` key `cf-collapsed`
- **Palette**: 8-slot array persisted in `localStorage` key `cf-palette`; context menu (`ctxMenu`) with Load/Save/Delete
- **No external JS, no external fonts** — system font stack only (`ui-monospace`, `system-ui`)

## Design

- Dark theme, CSS custom properties defined on `:root`
- Fonts: `ui-monospace` (monospace labels/values), `system-ui` (body) — system fonts, no CDN
- Accent color: `#a78bfa` (violet)
- Max width: 420px, centered
- PWA: `display-mode: standalone` hides the header
