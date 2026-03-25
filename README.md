# Color Full

A single-file color translator that lives at the OS app level — not buried inside a design tool.

## Why

Color conversion is a small task I do constantly, across many contexts: writing CSS, checking accessibility contrast, communicating with designers, picking brand values. Every tool that does this is a *feature* inside a larger app — a panel in Figma, a plugin in Sketch, a tab in a browser DevTools drawer.

The friction of navigating *into* an app to reach a feature you use all the time adds up. Color Full treats color conversion as a first-class app, not an afterthought. Open it the same way you'd open a calculator.

## What it does

Converts colors between four formats simultaneously, in real time:

| Format | Range |
|--------|-------|
| **HEX** | `#rrggbb` / `#rrggbbaa` |
| **RGBA** | 0–255 (RGB), 0–1 (alpha) |
| **HSLA** | H 0–360°, S/L 0–100% |
| **HSBA / HSV** | H 0–360°, S/B 0–100% |

Type into any field, drag any slider — all other formats update instantly. Each section has a copy button. A color chip at the top shows the live color and opens the native OS color picker on click.

A palette row holds up to 8 saved colors. Click a swatch to load it. Right-click for save, load, and delete.

## Install

Served as a plain HTML file — no build step, no dependencies, no framework. Open it in Chrome and install it as a PWA:

1. Serve locally (e.g. via Apache, `npx serve`, or any static server)
2. Open in Chrome
3. Click the **Install** button in the address bar
4. It appears in your dock / app launcher like any other app

Once installed it opens in its own window, with no browser chrome, at a fixed size — exactly like a native utility app.

## Architecture

- **Single source of truth** — `state = { r, g, b, a }` (RGBA floats 0–1)
- **Render loop** — `render(source)` updates all fields from state, skipping the active input section to avoid cursor jumps
- **Color math** — pure JS: `rgbToHsl`, `hslToRgb`, `rgbToHsb`, `hsbToRgb`, `rgbaToHex`, `hexToRgba`
- **No external JS** — system font stack, no CDN dependencies
- **One file** — `index.html` is the entire app
