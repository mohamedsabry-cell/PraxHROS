# Praxion Logo v2 — Production Package

**Version:** 2.0  **Date:** 2026-04-17  **Owner:** Sabry / PraxionHR Brand

Production-ready asset bundle for the approved v2 logo. This package is the source of truth for all external-facing use. **Do not redesign or edit these files** — open a new version (`v3`) if changes are needed.

---

## What's inside

```
v2-production/
├── svg/          True-vector source (open in any vector editor)
├── pdf/          Print-ready vector (CMYK-safe colors, embeds everywhere)
├── eps/          Legacy industry-standard vector (PS Level 3 EPS)
├── png/
│   ├── 1024/     transparent/ + white-bg/
│   ├── 2048/     transparent/ + white-bg/
│   ├── 4096/     transparent/ + white-bg/ (4K)
│   └── 8192/     transparent/ + white-bg/ (8K, for billboards / large print)
└── README.md     (this file)
```

**Total: 88 deliverable files** — 8 SVG, 8 PDF, 8 EPS, 64 PNG.

---

## The 8 variants

| Filename base | When to use |
|---|---|
| `praxion-full`            | Default. Full horizontal lockup on light backgrounds. |
| `praxion-inverse`         | Full lockup on dark/navy backgrounds. |
| `praxion-mono-black`      | Single-color print, faxes, embossing, small-space mono use. |
| `praxion-mono-white`      | Single-color on dark imagery or colored backgrounds. |
| `praxion-icon-full`       | Square/social avatars, favicons, app icons (color). |
| `praxion-icon-inverse`    | Square mark on dark backgrounds. |
| `praxion-icon-mono-black` | Mono icon on light. |
| `praxion-icon-mono-white` | Mono icon on dark. |

---

## Color specifications

| Token | HEX | RGB | CMYK (approx) | Role |
|---|---|---|---|---|
| **Navy**        | `#0B1437` | 11, 20, 55    | 98, 92, 36, 61 | Wordmark, outlines, anchor |
| **Signal Blue** | `#2F6BFF` | 47, 107, 255  | 82, 60, 0, 0   | Top vertex dot |
| **Ion Teal**    | `#19D3C5` | 25, 211, 197  | 70, 0, 30, 0   | Bottom-right dot, right cube face tint, gradient bar |
| **Amber**       | `#F59E0B` | 245, 158, 11  | 0, 40, 100, 0  | Bottom-left vertex dot |
| **White**       | `#FFFFFF` | 255, 255, 255 | 0, 0, 0, 0     | Mono-white, highlight face |

Print/CMYK users: send the **PDF** or **EPS** for best fidelity. For Pantone matching, request spot-color versions separately.

---

## Minimum sizes & clear space

- **Minimum digital size:** Full lockup 120 px wide · Icon 24 px
- **Minimum print size:** Full lockup 30 mm wide · Icon 8 mm
- **Clear space:** Protect the logo with a margin equal to the cube-mark height on all sides. Nothing — text, imagery, edges — should intrude on that space.

---

## Usage rules

### Do
- Use the provided vector formats (SVG/PDF/EPS) whenever possible.
- Use `-inverse` on dark or photographic backgrounds.
- Use `-mono-black` or `-mono-white` for single-color reproduction.
- Keep the clear-space margin intact.

### Don't
- Don't recolor, rotate, skew, or re-proportion the logo.
- Don't apply drop shadows, glows, or filters (the mark already carries built-in dimensionality).
- Don't place the color lockup on busy imagery — use an inverse or mono variant instead.
- Don't re-export PNG above 8192 px (upscale from the vector files).
- Don't substitute the wordmark typeface.

---

## Export instructions

### For web / UI
Use `svg/praxion-*.svg` directly — they're the smallest, sharpest, and scale infinitely. Fallback: `png/2048/*` for platforms that don't accept SVG.

### For print
- Business cards, letterhead, brochures → `pdf/praxion-*.pdf`
- Legacy print shops that require EPS → `eps/praxion-*.eps`
- Large-format (banners, trade-show, billboards) → `png/8192/*` or the vector files

### For presentations (PowerPoint / Keynote / Google Slides)
Insert `png/2048/transparent/praxion-full.png` or drag the SVG in directly if your tool supports it.

### For social media
- Profile icons → `png/1024/transparent/praxion-icon-full.png` or `-inverse`
- Cover / banner imagery → `png/4096/*` full lockups

---

## Technical notes

- All vector files were rendered through Cairo's native PDF/PS/SVG surfaces — they are true vector, not rasterized.
- All PNGs carry 300 DPI metadata.
- The cube mark uses layered rhombus faces (no raster filters), so SVG renders identically in every browser and vector tool.
- Shadows are built from stacked ellipses (not SVG `<filter>` blur), ensuring parity across ImageMagick, librsvg, Chromium, and native design tools.
- The `Icon` zero-byte markers visible on macOS are Finder custom-icon metadata; they are harmless and invisible in the Finder UI.

---

## Change log

| Date | Change |
|---|---|
| 2026-04-17 | v2 production package shipped. SVG + PDF + EPS + PNG (1024 / 2048 / 4096 / 8192) for all 8 variants. |
| 2026-04-16 | v2 design approved: 3D cube faces, amber accent at bottom-left vertex, teal-infused right face, ground shadow. |

---

## Contact

Brand owner: **Sabry — mohamed.sabry@praxionhr.com**
Source files & history: `/Brand/logos/` (v1) and this folder (v2).
