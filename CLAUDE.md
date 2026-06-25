# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**Puckilander** — a 31-day German-language gift/affirmation calendar (24 Jun – 24 Jul 2026), starting 2026-06-24. Single-file static web app, no build step, no dependencies. Deployed at https://miebach-timo.github.io/puckilander/ (GitHub Pages).

## Running

Open `index.html` directly in a browser, or serve it locally:

```powershell
npx serve .
# or
python -m http.server 8080
```

No build, no compile, no install step.

## Architecture

Everything lives in `index.html` (~780 lines): a `<style>` block, minimal HTML scaffold, then a vanilla JS block (`CARDS` array at line ~424) that builds the DOM.

**Card system**: 31 hardcoded objects in `CARDS` (emoji + German affirmations). Active day index = `Math.floor((now - START_DATE) / 86400000)`, clamped to `[0, CARDS.length-1]`. Future cards render but stay locked.

**State**: browser storage only.
- `localStorage`: `puckilander_revealed_${i}`, `puckilander_bg_YYYY-MM-DD`, `puckilander_gif_${i}`
- `sessionStorage`: `puckilander_unlocked` (password gate, cleared on tab close)

**Password gate**: SHA-256 via WebCrypto, compared against `PW_HASH` (~line 724). No plaintext. Current password: `eileen` — to change it, replace `PW_HASH` with the new hash. The gyroscope permission is requested *synchronously* on the login button click — iOS requires the `DeviceOrientationEvent.requestPermission` call to originate from a user gesture.

**Background**: pure CSS — the `.sky` aurora gradient (fixed, `inset:0`) plus a matching `body` gradient that camouflages any iOS standalone safe-area seam. No background-photo API.

**API** (key embedded in the file):
- Klipy — animated GIF per card, lazy-loaded, cached per card index

**3D tilt**: inline `style.transform` on `#tilt-wrap` — gyroscope on mobile, mousemove on desktop. **Do not add CSS animations to `#tilt-wrap`** — inline style and CSS animations fight over `transform`. Bob/float animations must live on a child wrapper.

## Open Tasks (see PROGRESS.md)

1. Top/bottom safe-area seam on iPhone standalone web app — mitigated by the `body` gradient camouflage (tune the stops in `html, body` to match `.sky` if a seam still shows on device).
