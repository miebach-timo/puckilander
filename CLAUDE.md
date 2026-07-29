# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**Puckilander** — a 31-day German-language gift calendar (24 Jun – 24 Jul 2026), starting 2026-06-24. Two switchable card sets ("seasons"): personal affirmations (rose) and animal facts (jade). Single-file static web app, no build step, no dependencies. Deployed at https://miebach-timo.github.io/puckilander/ (GitHub Pages).

## Running

Open `index.html` directly in a browser, or serve it locally:

```powershell
npx serve .
# or
python -m http.server 8080
```

No build, no compile, no install step.

## Architecture

Everything lives in `index.html`: a `<style>` block, minimal HTML scaffold, then a vanilla JS block that builds the DOM. Find things by symbol (`CARDS_LOVE`, `SEASONS`, `PW_HASH`) rather than by line number.

**Seasons**: `SEASONS` is the registry — each entry has `id`, `label`, `theme`, `themeColor`, a `cards` array and a GIF query source. Both seasons share `START_DATE` and the same unlock rhythm. Adding a third season means appending one entry plus a `:root[data-season="…"]` CSS block; nothing else needs touching.

**Card system**: 31 hardcoded objects per season (`CARDS_LOVE`, `CARDS_ANIMALS`). Active day index = `Math.floor((now - START_DATE) / 86400000)`, clamped per season by `activeIdxFor(season)`. Future cards render but stay locked. `.back-msg` is clamped to 4 lines — keep `msg` under ~150 characters or it gets cut off.

**Theming**: every season colour is a CSS custom property in `:root`; a season overrides them in `:root[data-season="jade"]`. **No rule outside those two blocks may hardcode a season colour** — otherwise a stray pink survives the switch to jade. `data-season` goes on `<html>`, not `<body>`: the html element paints the canvas behind the iOS safe-areas, so a `--deep` override scoped to body would leave the old colour showing in the seam.

**State**: browser storage only.
- `localStorage`: `puckilander_revealed_${i}` and `puckilander_gif_${i}` (season 1, kept unprefixed for backwards compatibility via `legacyKeys`), `puckilander_${seasonId}_revealed_${i}` / `_gif_${i}` (every other season), `puckilander_season` (last picked season)
- `sessionStorage`: `puckilander_unlocked` (password gate, cleared on tab close)

Reveal state is deliberately per-season — switching seasons must never reset cards. Go through `storeKey(season, i)` / `gifKey(season, i)`, never build a key inline.

**Password gate**: SHA-256 via WebCrypto, compared against `PW_HASH`. No plaintext. Current password: `eileen` — to change it, replace `PW_HASH` with the new hash.

**Background**: pure CSS — the `.sky` aurora gradient (fixed, `inset:0`) over an `html, body` fill of `var(--deep)`. No background-photo API.

**API** (key embedded in the file):
- Klipy — animated GIF per card, lazy-loaded, cached per season + card index. Season 1 resolves its search term from the `GIF_QUERIES_LOVE` emoji table; later seasons put a `gif` string on the card itself, which takes precedence.

**3D tilt**: inline `style.transform` on `#tilt-wrap`, mousemove on desktop. **Do not add CSS animations to `#tilt-wrap`** — inline style and CSS animations fight over `transform`. Bob/float animations must live on a child wrapper.

**Season picker**: custom glass dropdown in the header, not a native `<select>` (the iOS picker wheel can't be styled). It needs `touch-action: manipulation` because `body` sets `touch-action: none`. `.scene`'s `padding-top` accounts for the picker row — bump it if the header grows again.

## Open Tasks (see PROGRESS.md)

1. Top/bottom safe-area seam on iPhone standalone web app — confirm on a real device.
