# Puckilander — Progress & Offene Aufgaben

## Fertig

- [x] Mobile-first Single-File App (`index.html`)
- [x] 31 Tageskarten mit persönlichen Nachrichten (24. Juni – 24. Juli 2026)
- [x] Kalender startet 24. Juni 2026 — täglich eine neue Karte
- [x] Card-Rise Entry Animation (Karte fliegt von unten rein)
- [x] Flip-Reveal Animation (CSS 3D flip beim Antippen)
- [x] Shimmer-Sweep auf unaufgedeckten Karten (alle 4s)
- [x] Sparkle-Ring (statischer Farbverlauf + Glow)
- [x] Bob Float Animation auf Center-Karte
- [x] 3-Slot Karussell mit Swipe + Tap Navigation
- [x] Gesperrte Zukünftige Karten sichtbar aber nicht aufdeckbar
- [x] Revealed-State in localStorage gespeichert
- [x] Aurora-Hintergrund mit CSS-Animationen (Sky, Clouds, Sparkles)
- [x] Unsplash API — täglich wechselndes Hintergrundbild
  - Gecacht in localStorage (`puckilander_bg_YYYY-MM-DD`)
  - Key: `7VyEvJL6dc87s_F-8iKhJ4JzEsrVtbZEsHhZq04Z-Ho`
- [x] Klipy GIF API — passende GIFs pro Karte auf der Rückseite
  - Lazy loaded, gecacht in localStorage (`puckilander_gif_N`)
  - Key: `tb7vKsBjDQBtMLMD77FjB8RsakHHLZtDY9k64MaCqMIaqFPilvOfgyGjfVBXBryd`
- [x] Navigations-Jitter behoben (Animationen während Slide eingefroren)
- [x] 3D Tilt Animation (Gyroscope mobile, Maus desktop) via `#tilt-wrap`
- [x] Header „Puckilander" nicht mehr kursiv
- [x] iPhone App Icon (`icon.png`, 180×180, 🌸 auf pink/violet Gradient)
  - `<link rel="apple-touch-icon">` + `<meta name="apple-mobile-web-app-title">`
- [x] `manifest.json` für Android/PWA
- [x] GitHub Pages Deployment
  - URL: https://miebach-timo.github.io/puckilander/
  - Repo: https://github.com/miebach-timo/puckilander (public)
- [x] Passwort-Screen
  - SHA-256 via WebCrypto API, kein Klartext
  - `sessionStorage` — einmal entsperrt bis Tab geschlossen
  - Standardpasswort: `eileen` (Hash in `index.html` bei `PW_HASH` ändern)
  - Gyro-Permission wird synchron beim Login-Button angefragt (iOS-Anforderung)
- [x] Center-Karte bleibt im 3D-Raum immer vorne (`translateZ(2px)`)

---

## Offene Punkte

- [ ] Rand oben/unten auf iPhone als Home-Screen-Webapp — abgefedert durch den `body`-Gradient (camoufliert die Safe-Area-Naht). Falls auf dem Gerät noch sichtbar: Gradient-Stops in `html, body` an die `.sky`-Töne angleichen.

## Entfernt

- [x] **Unsplash-Anbindung komplett entfernt** — lief auf iOS nie zuverlässig (zeigte nur den Fallback-Verlauf). `.sky`-Aurora ist jetzt alleiniger Hintergrund. (`#bg-photo`, `#photo-credit`, `UNSPLASH_KEY`, `BG_QUERIES`, `loadDailyPhoto` raus)

## Erledigt seit letztem Stand

- [x] Sparkle-Ring schwebt jetzt synchron mit der Karte (Bob-Animation auf `.bob-host`, umschließt Ring + Flip-Shell)
- [x] Lock-Countdown dynamisch: „In einem Tag / In zwei Tagen … verfügbar" statt statisch „Ab morgen verfügbar"
- [x] Gyro-Effekt auf iPhone funktioniert (Permission-Fix bestätigt)
- [x] Karten-Overlay-Sync korrigiert (`translateZ(2px)` entfernt → `z-index: 1`)
- [x] Tag 2 Text + Zitronen-Emoji/GIF (🍋) aktualisiert
