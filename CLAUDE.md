# color-full

A single-file color translator web app — no build step, no dependencies, no framework.

## Project structure

- `index.html` — the entire app (HTML + CSS + JS in one file)

## What it does

Converts colors between formats in real time:
- **HEX** (`#rrggbb` / `#rrggbbaa`)
- **RGBA** (0–255 / 0–1 alpha)
- **HSLA** (hue 0–360°, saturation/lightness 0–100%)
- **HSBA / HSV** (hue 0–360°, saturation/brightness 0–100%)

Each format section has text inputs, sliders, and a copy button. A color chip at the top shows the active color and opens the native OS color picker on click.

## Architecture

- **Single source of truth**: `state = { r, g, b, a }` (RGBA floats)
- **Render loop**: `render(source)` updates all fields from state, skipping the source section to avoid cursor jumps. `updating` flag prevents re-entrant renders.
- **Color math**: pure JS functions — `rgbToHsl`, `hslToRgb`, `rgbToHsb`, `hsbToRgb`, `rgbaToHex`, `hexToRgba`
- **Slider backgrounds**: `updateSliderBgs()` sets gradient backgrounds on each range input to visualize the color channel it controls
- **No external JS** — fonts loaded from Google Fonts CDN only

## Design

- Dark theme, CSS custom properties defined on `:root`
- Fonts: `DM Mono` (monospace labels/values), `DM Sans` (body)
- Accent color: `#a78bfa` (violet)
- Max width: 420px, centered
