# Puckilander — Progress & Offene Aufgaben

## Fertig

- [x] Mobile-first Single-File App (`index.html`)
- [x] 31 Tageskarten pro Season (24. Juni – 24. Juli 2026)
- [x] Kalender startet 24. Juni 2026 — täglich eine neue Karte
- [x] Card-Rise Entry Animation (Karte fliegt von unten rein)
- [x] Flip-Reveal Animation (CSS 3D flip beim Antippen)
- [x] Shimmer-Sweep auf unaufgedeckten Karten (alle 4s)
- [x] Sparkle-Ring (statischer Farbverlauf + Glow)
- [x] Bob Float Animation auf Center-Karte
- [x] 3-Slot Karussell mit Swipe + Tap Navigation
- [x] Gesperrte Zukünftige Karten sichtbar aber nicht aufdeckbar
- [x] Revealed-State in localStorage gespeichert (pro Season getrennt)
- [x] Aurora-Hintergrund mit CSS-Animationen (Sky, Clouds, Sparkles) — alleiniger Hintergrund
- [x] Klipy GIF API — passende GIFs pro Karte auf der Rückseite
  - Lazy loaded, gecacht in localStorage (`puckilander_gif_N`)
  - Key: `tb7vKsBjDQBtMLMD77FjB8RsakHHLZtDY9k64MaCqMIaqFPilvOfgyGjfVBXBryd`
- [x] Navigations-Jitter behoben (Animationen während Slide eingefroren)
- [x] 3D Tilt Animation (Maus desktop) via `#tilt-wrap`
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
- [x] Center-Karte bleibt im 3D-Raum immer vorne (`z-index: 1`)

---

## Offene Punkte

- [ ] iOS Home-Screen-Webapp: Safe-Area oben/unten am echten Gerät final bestätigen — in **beiden** Seasons. Falls noch eine Kante: `--deep` der jeweiligen Season an die untere `.sky`-Kante angleichen.
- [ ] Klipy-GIFs am echten Gerät prüfen: `loadCardGif` liest die Antwort im Tenor-Schema (`media_formats.tinygif`), der Host ist aber `api.klipy.com`. Aus der Entwicklungsumgebung nicht testbar (kein Netzzugang). Falls die GIFs nicht laden, ist das ein Altbestand, kein Season-Problem.

## Entfernt

- [x] **Unsplash-Anbindung komplett entfernt** — lief auf iOS nie zuverlässig (zeigte nur den Fallback-Verlauf). `.sky`-Aurora ist jetzt alleiniger Hintergrund. (`#bg-photo`, `#photo-credit`, `UNSPLASH_KEY`, `BG_QUERIES`, `loadDailyPhoto` raus)

## Erledigt seit letztem Stand

- [x] **Zweite Krabbe**: blau, mit Partyhut (Bommel + Streifen), wohnt gleichberechtigt im Puckilander
  - Krabben-Code zur Fabrik `makeCrab()` umgebaut — jede hält ihren Zustand im eigenen Closure, sonst liefen beide im Gleichschritt
  - Eigener Rhythmus über `opts` (Tempo, Pausen, Kletterneigung); eine gemeinsame rAF-Schleife für beide
  - Elementgröße kommt aus dem Pixelraster, nicht aus dem CSS — der Hut macht die blaue schlicht höher
- [x] **Pflanzen am unteren Rand**: verstreute Grasbüschel und Blümchen als Pixelgrafik
  - Feste Positionstabelle statt Zufall, mit Lücken zwischen den Gruppen; Mitte licht, damit die Tagesanzeige frei bleibt
  - Sanftes Wiegen, jede Pflanze mit eigenem Takt
  - Farben folgen der Season (`--plant-*`, `--bloom`) über CSS-Klassen an den Rechtecken; auf Jade deutlich dunkler, sonst verschwinden sie im grünen Hintergrund
  - Die Krabben behalten Orange und Blau — Maskottchenfarben sollen sich beim Season-Wechsel gerade *nicht* ändern

- [x] **Adventskalender-Modus**: kein festes Startdatum mehr im Code
  - Jede Season merkt sich ihren Start (`puckilander_<id>_start`) und schaltet ab da täglich eine Karte frei
  - Tagesrechnung von Mitternacht zu Mitternacht und **gerundet**, damit eine Zeitumstellung (23- bzw. 25-Stunden-Tag) keinen Tag verschiebt
  - Startdatum in der Zukunft (verstellte Uhr) landet auf Tag 1, kaputtes Datum wird neu gestempelt
- [x] **🎲-Button im Header**: setzt die *aktuelle* Season zurück und mischt sie neu
  - Fragt vorher nach; die andere Season bleibt unberührt
  - Ab dem Druck läuft der Kalender wieder ab Tag 1
  - Kartenreihenfolge liegt in `puckilander_<id>_order`; ohne Eintrag gilt die ursprüngliche Reihenfolge
  - Aufgedeckt-Status hängt am **Tag**, GIFs an der **Karte** — beim Mischen wandern Karten zwischen Tagen, das GIF bleibt bei seiner Karte

- [x] **Krabbe als Maskottchen**: kleine orange Pixel-Krabbe, die im Puckilander wohnt
  - Läuft am unteren Rand hin und her, klettert auf die Karte, macht Pausen und winkt
  - Auch im Login sichtbar
  - Sprite aus dem `ART`-Zeichenraster erzeugt (ein Zeichen = ein Pixel) — zum Umzeichnen reicht das Raster
  - `pointer-events: none`, fängt also weder Kartentipp noch Swipe ab
  - Bei `prefers-reduced-motion` sitzt sie still am Boden
- [x] Neu hinzugefügte Seasons starten komplett zugedeckt (`autoRevealPast` nur bei Season 1) — vorher waren alle Tierkarten sofort offen, weil ihre Tage schon in der Vergangenheit liegen
- [x] **Season-System**: Dropdown im Header schaltet zwischen Karten-Sets um
  - `SEASONS`-Registry (`id`, `label`, `theme`, `themeColor`, `cards`); neue Season = ein Eintrag + ein `:root[data-season="…"]`-CSS-Block
  - Reveal- und GIF-Status pro Season getrennt (`puckilander_<season>_revealed_N`); Season 1 behält ihre alten, unpräfixierten Keys, damit nichts zurückgesetzt wird
  - Zuletzt gewählte Season wird in `puckilander_season` gemerkt
  - Eigenes Glas-Dropdown statt `<select>` (natives iOS-Picker-Rad ist nicht stylebar)
- [x] **Season 2 „Tierwelt"**: 31 Tier-Fakten auf Deutsch, Jade/Sage-Farbwelt
  - Gleicher Tages-Rhythmus wie Season 1 (gleiches `START_DATE`)
  - GIF-Suchbegriff steht direkt auf der Karte (`gif`), nicht in einer Emoji-Tabelle
- [x] Alle Season-Farben als CSS-Custom-Properties in `:root`; kein hartcodierter Farbwert mehr außerhalb der Theme-Blöcke
- [x] `data-season` sitzt auf `<html>`, nicht `<body>` — sonst bliebe die iOS-Safe-Area in der alten Farbe stehen
- [x] Login-Screen nutzt denselben Hintergrund wie das Hauptinterface (Overlay transparent, Aurora scheint durch; Vordergrund via `body.locked` ausgeblendet)
- [x] Tag 4 Text neu: „Mit dir isset einfach schöner, selbst bei 40 Grad und Körperkontakt."
- [x] Kartentexte Korrekturlauf: Rechtschreib-/Satzzeichenfehler behoben (Tag 6, 9, 12, 13, 18, 19, 20, 21, 24, 25, 31) — bewusst gelassener Slang/Spitznamen (smol, beb, isset, Wallah, frfr …)
- [x] Durchgängiger Hintergrund ohne Kante: `html, body` bekommen eine Volltonfarbe (`var(--deep)`), die die iOS-Safe-Area füllt — ein Gradient-Image tut das nicht
- [x] Sparkle-Ring schwebt jetzt synchron mit der Karte (Bob-Animation auf `.bob-host`, umschließt Ring + Flip-Shell)
- [x] Lock-Countdown dynamisch: „In einem Tag / In zwei Tagen … verfügbar" statt statisch „Ab morgen verfügbar"
- [x] Karten-Overlay-Sync korrigiert (`translateZ(2px)` entfernt → `z-index: 1`)
- [x] Tag 2 Text + Zitronen-Emoji/GIF (🍋) aktualisiert
- [x] Repo aufgeräumt: Debug-Screenshots gelöscht, `.gitignore` (`*.png` außer `icon.png`, `.playwright-mcp/`)
