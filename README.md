# Client‑side Collage Maker

A single‑file, client‑only web app to arrange multiple images into a grid/collage and export as PNG. No servers, APIs, keys, or build tools — just open `index.html` in your browser.

- Drag & drop or browse to add images
- Reorder by dragging thumbnails
- Auto layout or Manual rows/columns
- Fit modes: `contain` (no crop), `cover` (crop to fill), `fill` (stretch)
- Independent `Padding X / Y`, `Corner radius`, and optional `Tight packing`
- Max canvas width/height with Auto clamp + `Export scale`
- Separate buttons for `Render` (preview) and `Download PNG` (export)

EXIF orientation is respected via `createImageBitmap({ imageOrientation: "from-image" })`, so photos appear upright.

---

## Quick start

1) Clone or download this repository  
2) Open `index.html` in any modern desktop browser (Chromium/Chrome, Edge, Firefox, Safari)  
3) Drop a few images or click “Browse”  
4) Tweak layout in the left sidebar  
5) Click “Render” to preview in the large canvas  
6) Click “Download PNG” to save the rendered collage

Tip: Use the “Load sample gradient tiles” link in the footer if you want to test without local images (procedural demo tiles are generated entirely in the browser).

---

## Features

- Client‑only: image processing happens locally in the browser
- Drag & drop or file picker; EXIF orientation aware
- Thumbnail list with drag‑to‑reorder and remove
- Layout
  - Mode: `Auto` (near‑square by count) or `Manual` (specify rows/cols)
  - Fit: `contain` (letterbox, no crop), `cover` (crop to fill, focal‑point‑ready), `fill` (stretch)
  - Independent `Padding X` (horizontal gutter) and `Padding Y` (vertical gutter)
  - Rounded corners (clip path) per tile
  - Tight packing (last row width matches filled cells)
- Canvas / Export
  - `Max width / height` as upper bounds with `Auto` clamp
  - `Export scale` (0.5×–4×) to scale pixel output
  - Background color
  - Separate `Render` vs `Download PNG` buttons

---

## Controls reference

Left sidebar sections:

### Images
- Drag photos into the dashed box or click **Browse**
- Reorder by dragging thumbnails; click **Remove** on a tile to delete

### Layout
- **Layout mode**  
  - Auto: picks a near‑square `cols = ceil(sqrt(n))`, `rows = ceil(n/cols)`  
  - Manual: reveals Rows/Columns inputs (grid honors your values)
- **Fit mode**  
  - Contain: show entire image inside cell (no crop) — letterbox/pillarbox allowed  
  - Cover: fill cell, cropping as needed (no stretching)  
  - Fill: stretch to tile (may distort)
- **Padding X / Padding Y**  
  Horizontal and vertical gaps independently (in pixels)
- **Corner radius**  
  Rounded tile corners (in pixels)
- **Tight packing**  
  Shrinks last row width to match remaining images (no trailing empty cells)

### Canvas
- **Max width / Max height + Auto**  
  When Auto is checked, output size is computed from grid/scale; otherwise numbers act as upper bounds (final canvas is uniformly clamped to fit)
- **Background**  
  Any CSS color (e.g. `#0b1020`, `white`, `hsl(220 30% 12%)`)
- **Export scale**  
  Multiply overall pixel dimensions (e.g. 1×, 2×) for higher‑resolution exports

### Export
- **Render**: draws to preview canvas only  
- **Download PNG**: saves the last rendered canvas as a PNG (`collage_[WxH].png`)

---

## How it works (high level)

- Images are decoded with `createImageBitmap({ imageOrientation: "from-image" })` so EXIF rotation is respected.
- The layout engine computes grid rows/cols (auto or manual) and canvas size, then derives per‑tile rectangles using integer math to avoid seams.
- A single `computeDrawRects` helper returns source/destination rectangles for a given fit mode, and `drawRoundedImage` applies a rounded‑rect clip and calls `drawImage` with `(sx, sy, sw, sh, dx, dy, dw, dh)`.
- The last row can optionally be “tight packed” so the row width matches the number of images in that row.

---

## Local development notes

This project is intentionally zero‑build:
- No npm, bundlers, or frameworks required
- All CSS/JS ships inline in `index.html`
- Just open the file in a modern browser

If you prefer a static server for local files:
```
# Python 3
python3 -m http.server 8080
# Then open http://localhost:8080/
```

---

## Roadmap / ideas

- Focal point UI for `cover` (currently centered but code is focal‑point ready)
- WebP or ZIP export
- Optional watermark or file‑name labels
- Additional auto‑layout flavors (e.g., fixed columns with auto rows)
- Persist settings between sessions

---

## Accessibility & keyboard

- The drop zone is focusable; keyboard users can press **Enter** on **Browse** to open the picker
- Sliders and number inputs are labeled; status text summarizes render size and memory estimate

---

## License

MIT — do as you wish. If you share improvements, a PR is welcome.
