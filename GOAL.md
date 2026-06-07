# GOAL — Liquidfin session

**Make Liquidfin the coolest, most robust Jellyfin theme — authentic iOS-26 *beta-1*
Liquid Glass with a crimson accent — that works correctly on EVERY target client.**

Base the robust mechanics on **ElegantFin** (battle-tested across these clients); take its
patterns that work and re-skin them as Liquid Glass. Never trade correctness for flash.

## Target clients (must all work)
- Jellyfin Desktop (macOS/Windows, CEF/Chromium) — must look genuinely COOL here
- Desktop Chrome / Edge / Brave
- Android app (WebView) + mobile Chrome
- **iOS app (WKWebView)** — images must load
- LG webOS / Samsung Tizen TV (older Chromium)

## Acceptance criteria
1. **Icons render everywhere** — never override Jellyfin's bundled icon font with a
   Google-hosted font that proxied servers block (that caused tofu). ✅ reverted.
2. **Poster/show images load on the iOS app** — no theme rule may clip/cover/zero card
   images (no ::after over the image layer, no calc() width override). ✅ reverted glare+density.
3. **Genuinely cool / premium on the macOS app** — visible Liquid Glass (refraction on
   centered overlays, rich frosted header, premium card hover), not plain.
4. **No black panels, no broken layouts** on any client (screen-edge chrome stays frosted,
   refraction is Blink-only + centered-only, opaque floor for no-blur engines, flat TV mode).
5. **Login, detail page, cards all polished** (no nested boxes, no error-red inputs, real
   buttons, clear logos, smooth backdrop fade).
6. **ElegantFin-quality polish, re-skinned as glass**: card hover "shine" on the OVERLAY
   layer (not the image), header gradient fade, clear logos, clean spacing.

## Working agreement (from this session's feedback)
- The demo can't be trusted for the user's real proxied server / native apps — **the user's
  own clients are the source of truth**. Ship conservative, ask them to verify, iterate.
- Verify glass rendering locally (headless Chromium panel render) before pushing.
- Prefer adopting an ElegantFin pattern over inventing a fragile one.

## Status / checklist
- [x] Revert active breakage (icon override, icon @import, card glare/overflow, card-density)
- [ ] Re-add card "shine" the ElegantFin way — on `.cardOverlayContainer::after` (hover-only,
      never over the image), re-skinned as a glass sheen
- [ ] Richer frosted header (gradient fade + specular), still no SVG refraction (edge-safe)
- [ ] Premium "My Media" hero tiles
- [ ] Re-verify glass locally; confirm with user on Desktop app + iPhone
