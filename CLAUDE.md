# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**Puckilander** — a 30-day German-language gift/affirmation calendar starting 2026-06-24. Single-file static web app, no build step, no dependencies.

## Running

Open `index.html` directly in a browser, or serve it locally:

```powershell
npx serve .
# or
python -m http.server 8080
```

No build, no compile, no install step.

## Architecture

Everything lives in `index.html` (~687 lines):

- **Lines 1–330**: `<style>` block — CSS variables, 3D transforms, all keyframe animations
- **Lines 331–360**: HTML structure — minimal DOM, mostly built by JS
- **Lines 361–687**: Vanilla JS — all app logic

**Card system**: 30 hardcoded objects with emoji + German affirmations. Day index = `Math.floor((now - START_DATE) / 86400000)`.

**State**: `localStorage` only. Keys: `puckilander_revealed_${i}`, `puckilander_bg_YYYY-MM-DD`, `puckilander_gif_${i}`.

**APIs** (keys embedded in the file):
- Unsplash — daily background photo, cached per date
- Klipy — animated GIF per card, cached per card index

**3D tilt**: inline `style.transform` on `#mainWrap` — gyroscope on mobile (iOS 13+ needs `DeviceOrientationEvent.requestPermission`), mousemove on desktop. **Do not add CSS animations to `#mainWrap`** — inline style and CSS animations fight over `transform`. Bob/float animations must live on a child wrapper.

## Open Tasks (PROGRESS.md)

1. Apple touch icon (`<link rel="apple-touch-icon">`)
2. GitHub Pages deployment
3. Password screen — SHA-256 client-side hash, `sessionStorage` to persist unlock
