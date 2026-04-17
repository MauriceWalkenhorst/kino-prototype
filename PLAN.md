# Kino Prototyp — Projektplan

## Ziel
Zeigen dass ein modernes Kino-Buchungssystem besser geht als UCI aktuell.
Cinematic Dark Design, single HTML-Datei, 5-seitiger Buchungs-Flow, vollständig mit Fake-Daten.

---

## Status
Design fertig, Prototyp vorhanden (`index.html`), bereit zur Weiterentwicklung.

---

## Pages (Flow)

1. **Homepage** — Hero (Nebula Rising), Filmgrid (5 Filme), Demnächst-Streifen
2. **Film Detail** — Hero-Backdrop, Filminfo, Vorstellungs-/Datum-Auswahl
3. **Sitzplatzauswahl** — Interaktiver Saalplan, Sidebar mit Preisübersicht
4. **Checkout** — Kontaktdaten, Zahlung (Karte/PayPal/Apple Pay), Promo-Code
5. **Bestätigung** — Ticket-Card mit QR-Code, Download/Kalender-CTAs

---

## Design Tokens

```
Background:   #09090b  (zinc-950)
Text:         #ffffff
Accent (CTA): #fdf300  (Gelb — UCI)
Hot (Badges): #eb265c  (Pink — UCI)
Info (IMAX):  #0092d2  (Blau — UCI)
Glass-Cards:  rgba(255,255,255,0.06)
Border:       rgba(255,255,255,0.1)
Font:         Lato (Google Fonts CDN)
Radius:       4px / 12px
Easing:       cubic-bezier(0.22, 1, 0.36, 1)  ← Spring Physics
```

---

## Tech Stack (aktuell)

- Einzige Datei: `index.html`
- Kein Build-Schritt, kein Framework
- Lato via Google Fonts CDN
- Vanilla JS für Navigation, Sitzplan, Interaktion
- Kein Tailwind (inline CSS für maximale Kontrolle)

---

## Design-Prinzipien (taste-skill)

- Kein `#000000` → zinc-950 (`#09090b`)
- Keine sticky Navbar → floating pill nav
- Animations: nur `transform` + `opacity`
- Spring-Easing: `cubic-bezier(0.22, 1, 0.36, 1)`
- Magnetic Button Effect (JS)
- Perpetual Float-Animation auf Hero-Poster
- Stagger-Animation beim Laden der Film-Cards
- Blur nur auf fixed Elementen

---

## Key Components

- **FilmCard:** Gradient-Poster, Hover-Overlay, Stagger-Animation
- **SeatMap:** 9 Reihen × 14 Sitze, Premium-Reihen D/E/F, max. 8 auswählbar
- **Ticket:** Perforation-Effekt, QR-Code (Pixel-Art CSS-Grid), gelbe Preisanzeige

---

## Fake-Daten (Filme)

```js
const films = [
  { id: 'nebula-rising', title: 'Nebula Rising', rating: 8.4, duration: '2h 18min', fsk: 12, genre: ['Sci-Fi', 'Action'], gradient: 'from #2d1b69 to #11998e' },
  { id: 'ritter', title: 'Der letzte Ritter', rating: 7.9, duration: '2h 45min', fsk: 16, badge: 'IMAX', gradient: 'from #3d0000 to #c0392b' },
  // ...
]
```

---

## Offene Ideen / Erweiterungen

- [x] Echte Film-API (TMDB) für Poster-Bilder
- [ ] Supabase für echte Buchungen
- [x] Dark/Light Mode Toggle
- [x] Standort-Auswahl (mehrere Kinos)
- [x] Barrierefreiheit (WCAG 2.2 AA)
- [x] PWA (manifest.json + Service Worker)
- [ ] Als Next.js App aufbauen (App Router, TypeScript, Tailwind)

---

## Vergleich mit UCI

| Feature | UCI heute | Prototyp |
|---------|-----------|----------|
| Designsprache | veraltet, blau/weiß | Cinematic Dark |
| Navigation | komplex, viele Ebenen | 3 Klicks bis Ticket |
| Sitzplan | funktional, ugly | interaktiv + premium |
| Mobile | eingeschränkt | mobile-first |
| Animations | keine | Spring Physics |
| Buchungsflow | fragmentiert | single page feel |
