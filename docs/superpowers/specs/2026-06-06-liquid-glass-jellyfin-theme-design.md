# Liquid Glass — Jellyfin Theme (Design Spec)

**Date:** 2026-06-06
**Status:** Implemented & verified live on Jellyfin 10.11 (demo, Chromium) — see `theme.css`, `README.md`, `preview/`
**Target:** Jellyfin **10.11.x** (latest stable; 10.11.10 as of May 2026), applied via Dashboard → Branding → Custom CSS.

## 1. Goal

A sleek, modern Custom CSS theme for Jellyfin that recreates **iOS 26 "Liquid Glass" — specifically the beta-1 character**: maximum translucency, bright specular edges, and real light refraction, before Apple frosted it up in later betas for legibility. Anchored by a **crimson** accent rendered the Liquid-Glass way (translucent, specular, glowing — not flat red).

It must look great and stay robust on the latest stable Jellyfin, degrade gracefully on clients/browsers that can't do the fancy bits, and ship as a single hosted file applied with one `@import`.

## 2. Aesthetic target — what makes beta-1 Liquid Glass

1. **High translucency.** Panels are strongly see-through; the library backdrop and content behind read clearly through the glass. Low glass tint alpha (~0.06–0.12), *not* heavy frost.
2. **Specular rim.** A bright, thin, light-catching edge highlight on every glass element — the single most recognizable cue.
3. **Lensing / refraction.** Content behind the glass subtly warps near the curved edges, as real curved glass bends light.
4. **Vibrancy.** Saturation/brightness boost of whatever is behind the glass (Apple's "vibrancy").
5. **Floating, concentric geometry.** Elements float as rounded pills/panels with generous continuous radii and detached shadows (like beta-1's Control Center and floating tab bars).

## 3. Technique — three layers, gracefully degrading

The theme is **CSS-only** (no DOM/JS access). It achieves the look in three layers so it looks great everywhere and spectacular where the engine allows.

- **Layer 1 — Translucent vibrancy (universal: Chromium, WebView, Safari, Firefox).**
  `backdrop-filter: blur(N) saturate(180%) brightness(1.1)` over a low-alpha glass fill. This is the base everyone sees.

- **Layer 2 — Specular rim + depth (universal).**
  A masked gradient-border pseudo-element (bright top-left rim fading around) plus `inset` highlight/shadow `box-shadow`s, and a soft outer shadow for float. No special browser support needed.

- **Layer 3 — Real refraction / lensing (progressive enhancement, Chromium only).**
  A self-contained SVG filter (`feTurbulence` fractalNoise → `feDisplacementMap`, low frequency + modest scale) embedded as a **`data:` URI inside the CSS** and referenced via `backdrop-filter: url("data:…#id")`. No HTML/DOM injection required. Wrapped in `@supports`; non-Chromium engines silently keep Layers 1–2.

Exact filter params, the `@supports` query, and the client/engine support matrix are finalized from the research workflow before implementation.

## 4. Palette / token system

Single `:root` block drives everything (retheme = edit tokens):

- **Base:** layered near-black gradient `#07070a → #0d0e13` with a faint crimson ambient bloom in corners; library backdrops bleed through behind glass.
- **Glass:** low-alpha fill (`rgba(255,255,255,0.06–0.10)` / dark variant), `1px rgba(255,255,255,0.10)` rim, `backdrop-filter` blur ~18–24px, radius 16–28px (pills full).
- **Accent (crimson):** `#E50914` base, `#ff2d42` hover; glow `rgba(229,9,20,0.45)` for hover shadows, focus rings, sliders, progress, active-nav pill, card-hover bloom; faint crimson in the background bloom.
- **Text:** `#f2f3f5`, dimmed 60%. **Motion:** ~0.25s `cubic-bezier(.2,.7,.2,1)`.
- **Font:** Plus Jakarta Sans (Inter fallback) imported via font `@import` at top of file.

## 5. Surfaces covered

Header & top tabs · left drawer + active-nav pill · cards (rounded glass, hover lift + crimson bloom, cleaned overlays/text) · home rows & section titles · item detail / hero (backdrop scrims, glass info, crimson Play) · buttons (primary = crimson liquid glass; icon buttons) · inputs / selects / search (glass fields, crimson focus) · dialogs / menus / action sheets (glass + blur) · now-playing bar + player OSD sliders (crimson progress/volume) · login / select-user (glass card over blurred backdrop) · played/unplayed & count indicators · scrollbars · text selection · toasts/loading.

## 6. Robustness & accessibility

- Target documented, stable selectors confirmed against the 10.11 source. **Avoid fragile hacks** (no `:nth-child` section reordering).
- Minimal, intentional `!important` — only where Jellyfin sets inline styles.
- **Legibility concession (beta-1's one flaw):** subtle text scrims behind card titles / hero text / header labels so content stays readable over bright posters. Exposed as a token — set to 0 for full beta-1 purity.
- `@media (prefers-reduced-motion)` disables hover transforms.
- **"Lite" toggle** (commented token) to drop blur/refraction for TV / low-end clients where `backdrop-filter` is expensive.

## 7. Delivery

```
jellyfin-theme/
├── theme.css      # token-driven Liquid Glass theme
└── README.md      # install (@import + paste fallback), token tuning, client support notes
```

Applied via one line in Dashboard → Branding → Custom CSS:

```css
@import url('https://cdn.jsdelivr.net/gh/<github-user>/jellyfin-theme@main/theme.css');
```

Plus a paste-ready fallback (the file's full contents) for users who don't want to host. jsDelivr caching/versioning (`@main` vs pinned tag) documented in the README.

## 8. Non-goals

- No light mode (glassmorphism doesn't translate).
- No layout restructure into a different feed shape (kept robust across updates).
- No plugin / Skin Manager packaging in v1 (pure Custom CSS).
- No per-element displacement-map PNGs (impractical for arbitrary card sizes; turbulence-based refraction is used instead).
