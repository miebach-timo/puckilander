# Handoff: 3D-Tilt-Integration in index.html

## Was zwei Sessions parallel gemacht haben

### Session A (Tilt)
Hat ans Ende des `<script>`-Blocks eine IIFE eingefügt, die eine 3D-Neigung auf `#mainWrap` anwendet:
- **Desktop**: `mousemove` → `el.style.transform = perspective(900px) rotateX(…) rotateY(…)`
- **Mobile Android**: `deviceorientation` → gleiches Transform
- **iOS 13+**: fragt Gyro-Berechtigung beim ersten `touchstart` an

Das Target-Element war `#mainWrap` via inline `style.transform`.

### Session B (Bob-Refactor)
Hat die Bob-Float-Animation umgehängt:

**Vorher (original):**
```css
.bob-wrap { width: 100%; height: 100%; border-radius: 28px; }
#mainWrap.revealed .bob-wrap {
  animation: bob 4s ease-in-out infinite;
}
@keyframes bob { 0%,100%{transform:translateY(0)} 50%{transform:translateY(-11px)} }
```

**Nachher (Session B):**
```css
/* Bob — floats whole card after reveal */
#mainWrap.revealed {
  animation: bob 4s ease-in-out infinite;
}
@keyframes bob { 0%,100%{transform:translateY(0)} 50%{transform:translateY(-11px)} }
```

Außerdem wurde im HTML das `<div class="bob-wrap">` entfernt.

---

## Der Konflikt

Beide Sessions schreiben `transform` auf `#mainWrap`:
- Session A: inline `style.transform` (Tilt)
- Session B: CSS-Animation `bob` (translateY) — **CSS-Animationen überschreiben inline styles auf der gleichen Property**

Ergebnis: Die Tilt-Rotation wird von der Bob-Animation überschrieben, sobald die Karte geöffnet ist.

---

## Fix

**Option 1 (empfohlen, minimaler Diff):** Bob-Animation zurück auf ein eigenes Wrapper-Element legen.

Im CSS ersetzen:
```css
/* Bob — floats whole card after reveal */
#mainWrap.revealed {
  animation: bob 4s ease-in-out infinite;
}
```
durch:
```css
.bob-wrap { width: 100%; height: 100%; border-radius: 28px; }
#mainWrap.revealed .bob-wrap {
  animation: bob 4s ease-in-out infinite;
}
```

Im HTML das `<div class="bob-wrap">` wieder einfügen (direkt innerhalb `.flipper`, um alle `.face`-Elemente wrappend):
```html
<div class="flipper" id="flipper">
  <div class="bob-wrap">          <!-- ← wieder hinzufügen -->
    <div class="face front">…</div>
    <div class="face back">…</div>
  </div>
</div>
```

Dann bleibt `#mainWrap` frei für den Tilt-inline-Transform der Session A, und die Bob-Animation läuft auf dem Kind-Element.

---

## Tilt-Code (Session A) — zum Einfügen vor `// ── Floating sparkles`

```js
// ── 3D Tilt ───────────────────────────────────────────────────────────────────
(function() {
  const el  = mainWrap;
  const MAX = 8;

  function tilt(rx, ry) {
    el.style.transition = 'transform 0.1s ease-out';
    el.style.transform  = `perspective(900px) rotateX(${rx}deg) rotateY(${ry}deg)`;
  }
  function reset() {
    el.style.transition = 'transform 0.5s ease-in-out';
    el.style.transform  = 'perspective(900px) rotateX(0deg) rotateY(0deg)';
  }

  // Mouse (desktop)
  document.addEventListener('mousemove', e => {
    const r = el.getBoundingClientRect();
    const rx = ((e.clientY - (r.top + r.height / 2)) / (r.height / 2)) * -MAX;
    const ry = ((e.clientX - (r.left + r.width  / 2)) / (r.width  / 2)) *  MAX;
    tilt(Math.max(-MAX, Math.min(MAX, rx)), Math.max(-MAX, Math.min(MAX, ry)));
  });
  document.addEventListener('mouseleave', reset);

  // Gyroscope (mobile)
  function attachGyro() {
    let baseB = null;
    window.addEventListener('deviceorientation', e => {
      if (baseB === null) baseB = e.beta ?? 45;
      const ry = Math.max(-MAX, Math.min(MAX, (e.gamma ?? 0) / 45 * MAX));
      const rx = Math.max(-MAX, Math.min(MAX, ((e.beta ?? baseB) - baseB) / 30 * MAX));
      tilt(rx, ry);
    });
  }

  if (typeof DeviceOrientationEvent !== 'undefined' &&
      typeof DeviceOrientationEvent.requestPermission === 'function') {
    // iOS 13+ — needs a user gesture
    document.addEventListener('touchstart', function req() {
      document.removeEventListener('touchstart', req);
      DeviceOrientationEvent.requestPermission()
        .then(s => { if (s === 'granted') attachGyro(); })
        .catch(() => {});
    }, { once: true });
  } else if (window.DeviceOrientationEvent) {
    attachGyro();
  }
})();
```
