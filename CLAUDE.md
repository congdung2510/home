# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single self-contained HTML file (`home-bao-hiem-so (2).html`) — a mobile-first home screen prototype for a Vietnamese digital-insurance app ("Bảo hiểm số"). No build system, no dependencies, no package manager. HTML, CSS (inline `<style>`), and JS (inline `<script>`) all live in the one file. UI text and code comments are in Vietnamese.

## Running / previewing

Open the file directly in a browser — there is nothing to build, install, or serve. Append `?theme=N` (N = 1, 2, or 3) to the URL to load a specific theme on first paint, which is used for taking independent per-theme screenshots.

## Architecture

The file is organized top-to-bottom as: **design tokens → themes → base/reset → layout → components** (CSS), then the markup, then per-feature JS modules. Section headers are numbered comments (`/* ---------- N. NAME ---------- */`) — use them to navigate.

**Theming is the central design idea.** Everything is driven by CSS custom properties:
- `:root` defines *primitives* (color scale, spacing, radius, type) plus *semantic tokens* (`--color-primary`, `--color-bg`, `--hero-text`, etc.) whose defaults equal Theme 1.
- `[data-theme="1|2|3"]` on `<html>` only overrides semantic tokens plus two **layout-flag** tokens: `--cat-layout` (arc / grid / list) and `--product-layout` (viettel / benefit / compact).
- Components render all three layout variants in the markup at once; the visible one is selected purely by CSS, e.g. `[data-theme="1"] .categories__view--arc{display:block}` hides the others. Switching theme swaps which variant is shown — no DOM is rebuilt for that.

**Naming:** BEM + semantic classes. No inline styles in markup (JS may set `style.transform`/`opacity` for animation). Utility prefix `u-` (e.g. `.u-visually-hidden`).

**JS modules** (bottom `<script>`) are independent IIFEs, one per feature, with no cross-dependencies except two hoisted `let` hooks (`initArc`, `initBannerDots`) that the theme switcher calls after a theme change so layout-dependent widgets re-measure for the newly visible theme:
- *Theme switcher* — toggles `root.dataset.theme`, honors `?theme=N`, exposes `window.__setTheme`.
- *Banner carousel* — auto-rotate every 4s + scroll-synced dots; stops on pointer interaction.
- *Arc carousel* (Theme 1 categories) — clones the category nodes into 5 copies for a seamless infinite ring, positions them along a parabola, supports click/drag, normalizes `center` back into the middle band to keep the ring filled.
- *Momentum drag-scroll* — applied to any horizontal track marked `[data-drag]`; adds velocity-based inertia and snap-to-item, and suppresses click after a drag.

## When editing

- Add a new color/spacing/type value as a token in `:root`, then reference it — don't hard-code literals in components.
- To change a theme's look, override semantic tokens inside its `[data-theme="N"]` block; add a new layout variant by defining a `--*-layout` flag value plus the matching `[data-theme="N"] .*--variant{display:block}` selector.
- Keep the 44px touch-target minimum (`--touch`) for interactive elements — this is a mobile app UI.
- The app shell is capped at `max-width:420px` (phone frame); design/test at that width.
