# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**Puckilander** — a 31-day German-language gift calendar in advent-calendar style: it starts the day it is first opened and unlocks one card per day from there. Two switchable card sets ("seasons"): personal affirmations (rose) and animal facts (jade). Single-file static web app, no build step, no dependencies. Deployed at https://miebach-timo.github.io/puckilander/ (GitHub Pages).

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

**Seasons**: `SEASONS` is the registry — each entry has `id`, `label`, `theme`, `themeColor`, a `cards` array and a GIF query source. Each season runs its own calendar. Adding a third season means appending one entry plus a `:root[data-season="…"]` CSS block; nothing else needs touching.

**Calendar clock**: there is no hardcoded start date. A season stamps `puckilander_${id}_start` the first time it is opened, and `activeIdxFor(season)` counts whole days from there, clamped to the last card. Day arithmetic is midnight-to-midnight and **rounded, not floored** — a DST change makes a day 23 or 25 hours long, which a floor-divide turns into an off-by-one. A start date in the future (user's clock is off) clamps to day 1; an unparseable one is re-stamped to today.

**Day vs card**: day N of the calendar shows `orderFor(season)[N]`, so the two are not the same number. Without a stored order the cards run in their authored sequence; the 🎲 reset button writes a shuffled permutation. Anything user-facing that says "Tag N" must use the **day**, never the card index. `orderFor` validates the stored array (right length, a real permutation) and falls back to authored order if it is corrupt.

**Card system**: 31 hardcoded objects per season (`CARDS_LOVE`, `CARDS_ANIMALS`). Future days render but stay locked; any unlocked day that is still face down can be opened, so a missed day can be caught up on. `.back-msg` is clamped to 4 lines — keep `msg` under ~150 characters or it gets cut off.

**Theming**: every season colour is a CSS custom property in `:root`; a season overrides them in `:root[data-season="jade"]`. **No rule outside those two blocks may hardcode a season colour** — otherwise a stray pink survives the switch to jade. `data-season` goes on `<html>`, not `<body>`: the html element paints the canvas behind the iOS safe-areas, so a `--deep` override scoped to body would leave the old colour showing in the seam.

**State**: browser storage only. Every key is namespaced per season.
- `localStorage`: `puckilander_${id}_day_${day}` (a day was opened), `puckilander_${id}_gif_${cardIdx}` (cached GIF), `puckilander_${id}_start` (calendar start, `YYYY-M-D`), `puckilander_${id}_order` (shuffled permutation), `puckilander_season` (last picked season)
- `sessionStorage`: `puckilander_unlocked` (password gate, cleared on tab close)

Note the deliberate split: **reveals are keyed by day, GIFs by card index.** A shuffle moves cards between days, so a GIF stays with its card while the day keeps its own opened-state. Go through `storeKey(season, day)` / `gifKey(season, cardIdx)`, never build a key inline.

Reveal state is per-season — switching seasons must never reset cards. Only the 🎲 button clears it, and only for the season that is currently open.

**Password gate**: SHA-256 via WebCrypto, compared against `PW_HASH`. No plaintext. Current password: `eileen` — to change it, replace `PW_HASH` with the new hash.

**Background**: pure CSS — the `.sky` aurora gradient (fixed, `inset:0`) over an `html, body` fill of `var(--deep)`. No background-photo API.

**API** (key embedded in the file):
- Klipy — animated GIF per card, lazy-loaded, cached per season + card index. Season 1 resolves its search term from the `GIF_QUERIES_LOVE` emoji table; later seasons put a `gif` string on the card itself, which takes precedence.

**3D tilt**: inline `style.transform` on `#tilt-wrap`, mousemove on desktop. **Do not add CSS animations to `#tilt-wrap`** — inline style and CSS animations fight over `transform`. Bob/float animations must live on a child wrapper.

**Crab mascots** (`#crab` orange, `#crab2` blue with a party hat): two pixel crabs that wander on their own — walk the bottom edge, climb onto the card, idle and wave, and are present on the login screen too. `makeCrab(el, opts)` is a factory: each crab keeps its own position and state in a closure, so they never move in lockstep. `opts` carries the art grid, palette, `walk`/`climb` speed, idle range and `cardChance` — that is where you tune one crab's personality. **One** `requestAnimationFrame` loop steps them all.

Sprites come from `pixelSprite`/`pixelRects` and a character grid (one char = one pixel); redraw the grid to change a crab. Element width/height are derived from the grid, not hardcoded — the party hat simply makes `#crab2` taller. Both crabs are anchored bottom-left and positioned with a negative `translate3d`.

JS owns `transform` on `.crab`, so **all CSS animations must live on child elements** — same constraint as `#tilt-wrap`. Both are `pointer-events: none` and must stay that way, otherwise they would swallow card taps and swipes while sitting on a card.

**Flora** (`#flora`): scattered pixel grass tufts and flowers along the bottom, from the same `pixelSprite` helper. Positions are a fixed `LAYOUT` table (percent + scale), not random, so the arrangement stays put across reloads and can be tuned by hand — keep visible gaps between clumps and leave the middle sparse, since the day counter sits there. Plants are scenery, so unlike the crabs they follow the season: their rects carry **classes** (`p-mid`, `p-bloom`, …) resolved against `--plant-*` / `--bloom` tokens, which is why `pixelSprite` takes a `'class'` mode. On jade the tokens must go noticeably darker or the plants vanish into the green background. `z-index: 18` keeps them under the day counter (20) and the crabs (25).

**Header popovers**: the season dropdown and the 🎲 reset confirmation share `bindPopper()` — only one open at a time, closing on outside click and Escape. Add a third the same way rather than wiring its own handlers.

**Season picker**: custom glass dropdown in the header, not a native `<select>` (the iOS picker wheel can't be styled). It needs `touch-action: manipulation` because `body` sets `touch-action: none`. `.scene`'s `padding-top` accounts for the picker row — bump it if the header grows again.

## Open Tasks (see PROGRESS.md)

1. Top/bottom safe-area seam on iPhone standalone web app — confirm on a real device.
