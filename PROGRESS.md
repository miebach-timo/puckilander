# Puckilander — Progress & Offene Aufgaben

## Fertig

- [x] Mobile-first Single-File App (`index.html`)
- [x] 30 Tageskarten mit Sprüchen auf Deutsch
- [x] Kalender startet 24. Juni 2026 — täglich eine neue Karte
- [x] Card-Rise Entry Animation (Karte fliegt von unten rein)
- [x] Flip-Reveal Animation (CSS 3D flip beim Antippen)
- [x] Shimmer-Sweep auf unaufgedeckten Karten (alle 4s)
- [x] Sparkle-Ring (statischer Farbverlauf + Glow)
- [x] Bob Float Animation auf Center-Karte (alle Cards)
- [x] 3-Slot Karussell mit Swipe + Tap Navigation
- [x] Gesperrte Zukünftige Karten sichtbar aber nicht aufdeckbar
- [x] Revealed-State in localStorage gespeichert
- [x] Aurora-Hintergrund mit CSS-Animationen (Sky, Clouds, Sparkles)
- [x] Unsplash API — täglich wechselndes Hintergrundbild (pink/girly)
  - Wechselt täglich, gecacht in localStorage (`puckilander_bg_YYYY-MM-DD`)
  - 10 rotierende Suchbegriffe
  - Key: `7VyEvJL6dc87s_F-8iKhJ4JzEsrVtbZEsHhZq04Z-Ho`
- [x] Klipy GIF API — passende GIFs pro Karte auf der Rückseite
  - Lazy loaded, gecacht in localStorage (`puckilander_gif_N`)
  - Key: `tb7vKsBjDQBtMLMD77FjB8RsakHHLZtDY9k64MaCqMIaqFPilvOfgyGjfVBXBryd`
- [x] Navigations-Jitter behoben (Animationen während Slide eingefroren)
- [x] 3D Tilt Animation (Gyroscope mobile, Maus desktop) via `#tilt-wrap` Wrapper
- [x] Header „Puckilander" nicht mehr kursiv

---

## Offene Aufgaben

### 1. iPhone App Icon (Add to Home Screen)
Wenn man die Seite in Safari als Verknüpfung auf den Homescreen legt, soll ein schönes Icon erscheinen statt dem Standard-Screenshot.

**Was zu tun ist:**
- `apple-touch-icon` PNG erstellen (180×180px, pink/girly, „P" oder Blüte)
- Im `<head>` einbinden:
  ```html
  <link rel="apple-touch-icon" href="icon.png">
  ```
- Optional: `manifest.json` für Android (PWA) mit Icon 192×192 + 512×512
- `<meta name="apple-mobile-web-app-title" content="Puckilander">` hinzufügen

---

### 2. GitHub Repo + GitHub Pages Deployment
Kostenlos veröffentlichen über GitHub Pages — keine Server, kein Hosting nötig.

**Was zu tun ist:**
1. Git-Repo initialisieren im Projektordner
2. Neues GitHub-Repo anlegen (privat oder öffentlich)
3. `index.html` pushen
4. In den Repo-Settings → Pages → Branch `main` / `/(root)` aktivieren
5. URL-Format: `https://[username].github.io/[repo-name]/`

**Hinweis:** Für private Nutzung → Repo auf **privat** setzen + Passwort-Screen (Aufgabe 3).  
GitHub Pages funktioniert auch mit privaten Repos (benötigt GitHub Pro) — alternativ: öffentliches Repo + Passwortschutz via JS reicht für persönliche Nutzung.

---

### 3. Passwort-Screen
Damit nicht jeder die Seite einsehen kann, soll beim ersten Öffnen ein Passwort abgefragt werden.

**Was zu tun ist:**
- Fullscreen Overlay beim Laden, der die App verdeckt
- Einfaches Eingabefeld + Bestätigen-Button
- Bei korrektem Passwort: Overlay ausblendet, Zustand in `sessionStorage` gespeichert (kein erneutes Abfragen in derselben Session)
- Passwort als Hash (SHA-256 via WebCrypto API) im Code gespeichert — kein Klartext
- Styling passend zum Rest: pink, glassmorphism, sanfte Fade-Animation

**Sicherheitshinweis:** Client-seitiger Passwortschutz hält nur neugierige Augen fern — kein echter Schutz gegen technisch versierte Nutzer. Für persönliche/private Nutzung völlig ausreichend.
