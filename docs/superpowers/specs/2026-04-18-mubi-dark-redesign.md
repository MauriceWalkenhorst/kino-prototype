# CINEX — MUBI-Dark Redesign Spec
**Datum:** 2026-04-18  
**Ziel:** Premium-Dark-Atmosphäre (MUBI/Netflix-Stil), mehr Tiefe, weniger Farb-Lärm, echte TMDB-Backdrops im Hero

---

## 1. Color & Token System

| Token | Alt | Neu |
|---|---|---|
| `--bg` | `#09090b` | `#0a0a0c` |
| `--surface` | *(neu)* | `#111114` |
| `--border` | `rgba(255,255,255,0.08)` | `rgba(255,255,255,0.05)` |
| `--text-dim` | `rgba(255,255,255,0.55)` | `rgba(255,255,255,0.40)` |
| `--accent` | `#fdf300` | bleibt — NUR auf primärem CTA-Button und Sternebewertung |
| `--hot` | `#eb265c` | **entfernt** |
| `--info` | `#0092d2` | **entfernt** |

Badges: alle `rgba(255,255,255,0.10)` mit weißem Text. IMAX = Outline-Badge (weißer Border, transparenter BG). NEU = weißer Outline-Badge.

---

## 2. Hero — Backdrop statt CSS-Orbs

- TMDB backdrop image (`/w1280{backdrop_path}`) als absolut positionierter `<img>` im Hero, `object-fit: cover`, volle Breite und Höhe
- Gradient-Overlay darüber: `linear-gradient(to right, #0a0a0c 40%, rgba(10,10,12,0) 70%), linear-gradient(to top, #0a0a0c 0%, rgba(10,10,12,0.3) 100%)`
- CSS-Orbs (`orb-1`, `orb-2`, `orb-3`) werden entfernt
- Hero-Canvas Radial-Gradient bleibt als Fallback wenn TMDB-Bild nicht lädt
- Floating Poster rechts bleibt, bekommt mehr `box-shadow` für Tiefe

---

## 3. Film-Datensystem (FILMS[])

Neues JS-Array `FILMS` ersetzt hartcodierte HTML-Daten:

```js
const FILMS = [
  {
    id: 'dune-2',
    title: 'Dune: Part Two',
    tmdbId: 693134,
    rating: 8.5, votes: '52k',
    duration: '2h 46min', fsk: 12,
    genres: ['Sci-Fi', 'Epos', 'Drama'],
    year: 2024,
    badge: 'Top',
    imax: true,
    desc: '...',
    trailerYtId: 'xI6whUDDVuU',
    gradient: 'linear-gradient(150deg,#2d1b69,#11998e)',
    showtimes: { standard: [...], imax: [...], ov: [...] }
  },
  // ... weitere 4 Filme
]
```

`currentFilmId` State-Variable steuert welcher Film in Hero + Detail gezeigt wird.  
Klick auf Film-Card → `setFilm(id)` → aktualisiert Hero-Backdrop + öffnet Detail.

---

## 4. Film-Cards

- Hintergrund: `var(--surface)` (#111114) statt Glass-Morphismus
- Border: `1px solid var(--border)` (kaum sichtbar)
- Hover: `transform: scale(1.03)` + leichter `box-shadow`, kein Glow
- Badges: monochrom (siehe Sektion 1)
- Card-Meta Sternebewertung: gelb (einzige Farbausnahme in Cards)

---

## 5. Demnächst-Strip

- Thumb-Bgs bleiben Gradients (kein Backdrop nötig für Coming-Soon)
- Genre-Labels: `rgba(255,255,255,0.35)` statt farbig
- Datum-Label: weißes Outline-Badge, kein farbiger Hintergrund

---

## 6. Scope — was NICHT geändert wird

- Sitzplan-Page: keine Änderungen
- Checkout-Page: keine Änderungen
- Bestätigungs-Page: keine Änderungen
- Layout und Schriftgrössen bleiben
- PWA, Service Worker, Trailer-Modal: unverändert

---

## Erfolgs-Kriterien

- Seite wirkt nach dem Laden ruhiger und edler als vorher
- Kein farbiger Badge mehr sichtbar (außer gelbe Sterne)
- Hero zeigt echtes Filmbild statt CSS-Blobs
- Alle 5 Film-Cards öffnen korrekte Film-Daten in der Detail-Seite
