# Plan: Palette Export (CSS) + Import

## Context
The palette currently stores up to 8 colors in localStorage as hex strings. The user wants to make palettes portable — downloadable as a CSS file and importable from one. This enables sharing palettes with other tools/devs and loading palettes from external sources.

## Export Format
CSS custom properties, saved as a `.css` file:

```css
/* Color Full Palette */
:root {
  --color-1: #6366f1;
  --color-2: #a78bfa;
  --color-3: null;
  ...
}
```
- Only non-null slots are written (empty slots are skipped)
- Property names: `--color-1` through `--color-8` (1-indexed)
- Values are whatever `rgbaToHex()` produces (includes alpha channel if a < 1)
- Download triggered via `<a download>` with a Blob URL

## Import Format
Parse any `.css` file for CSS custom properties whose values are valid hex colors:
- Regex: `/--[\w-]+\s*:\s*(#[0-9a-fA-F]{3,8})/g`
- Take the first 8 matches, map them into palette slots 0–7
- Empty palette slots remain null for any unmatched positions
- Invalid or non-color properties are ignored

## UI Changes

### Palette header (HTML)
Add Export and Import buttons next to the label, matching the existing `.copy-btn` style:
```html
<div class="palette-header">
  <span class="format-label">Palette</span>
  <div style="display:flex;gap:4px">
    <button class="copy-btn" onclick="importPalette()">Import</button>
    <button class="copy-btn" onclick="exportPalette()">Export</button>
  </div>
</div>
```

Add a hidden `<input type="file" accept=".css">` for the import file picker.

## JS Functions

### `exportPalette()`
```js
function exportPalette() {
  const lines = palette
    .map((c, i) => c ? `  --color-${i + 1}: ${c};` : null)
    .filter(Boolean);
  const css = `/* Color Full Palette */\n:root {\n${lines.join('\n')}\n}\n`;
  const a = document.createElement('a');
  a.href = URL.createObjectURL(new Blob([css], { type: 'text/css' }));
  a.download = 'palette.css';
  a.click();
  URL.revokeObjectURL(a.href);
}
```

### `importPalette()`
Triggers the hidden file input, then on `change`:
```js
function importPalette() {
  $('paletteFileInput').click();
}

$('paletteFileInput').addEventListener('change', e => {
  const file = e.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = ev => {
    const matches = [...ev.target.result.matchAll(/--[\w-]+\s*:\s*(#[0-9a-fA-F]{3,8})/g)];
    const colors = matches.slice(0, PALETTE_SIZE).map(m => m[1]);
    palette = [...colors, ...new Array(PALETTE_SIZE - colors.length).fill(null)];
    savePalette();
    renderPalette();
  };
  reader.readAsText(file);
  e.target.value = '';
});
```

## Files Modified
- `/Users/brian.mcconnell/Sites/color-full/index.html` — only file in the project

## Verification
1. Save 2–3 colors to the palette
2. Click Export → `palette.css` downloads with correct `--color-N` properties
3. Clear all swatches (delete each)
4. Click Import → pick the downloaded file → swatches repopulate correctly
5. Test with alpha color (a < 1) — hex should include alpha byte in export and round-trip correctly
6. Test importing a CSS file with more than 8 color vars — only first 8 are loaded
7. Test importing a CSS file with non-color custom properties — they are ignored
