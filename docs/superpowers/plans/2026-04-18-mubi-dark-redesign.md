# CINEX MUBI-Dark Redesign — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Redesign `index.html` to a premium MUBI-Dark aesthetic — monochrome badges, real TMDB backdrops in the hero, a JS film data model, and dynamic detail pages per film.

**Architecture:** Single `index.html` file. All changes are CSS, HTML, and vanilla JS within that file. A `FILMS[]` array replaces hardcoded film data. `setFilm(id)` drives hero + detail page updates. `fetchTmdb()` returns both poster and backdrop URLs per film.

**Tech Stack:** Vanilla JS, inline CSS, TMDB API (`api.themoviedb.org/3`)

---

## File Map

| File | Changes |
|---|---|
| `index.html` lines 15–28 | CSS token variables |
| `index.html` lines 169–177 | Badge CSS |
| `index.html` lines 200–206 | Remove orb CSS |
| `index.html` lines 183–232 | Hero CSS — add backdrop img, remove pseudo-elements |
| `index.html` lines 854–892 | Hero HTML — add `<img id="hero-backdrop">`, remove orb divs, add poster-bg div |
| `index.html` lines 900–963 | Film card HTML — add `data-film-id`, fix onclick |
| `index.html` lines 1031–1108 | Detail page HTML — add dynamic IDs |
| `index.html` lines 1359–1403 | JS — replace fetchPoster/loadAllPosters with fetchTmdb/loadAllTmdb |
| `index.html` after line 1403 | JS — add FILMS[], setFilm(), updateDetailPage(), buildShowtimes() |

---

## Task 1: Update CSS Tokens

**Files:** `index.html` (`:root` block, ~lines 15–28)

- [ ] **Replace the `:root` block** with:

```css
:root {
  --bg: #0a0a0c;
  --text: #fff;
  --accent: #fdf300;
  --surface: #111114;
  --glass: rgba(255,255,255,0.05);
  --glass-hover: rgba(255,255,255,0.09);
  --border: rgba(255,255,255,0.05);
  --font: 'Lato', 'Helvetica Neue', Helvetica, Arial, sans-serif;
  --r: 4px;
  --r-lg: 12px;
  --spring: cubic-bezier(0.22, 1, 0.36, 1);
}
```

Note: `--hot` and `--info` are removed entirely.

- [ ] **Verify in browser:** Background should look very slightly deeper/darker than before. No layout changes.

- [ ] **Commit:**
```bash
git add index.html
git commit -m "style: update tokens to MUBI-dark palette, remove hot/info vars"
```

---

## Task 2: Monochrome Badge System

**Files:** `index.html` (badge CSS ~lines 169–177, badge HTML ~lines 862, 907, 920, 945, 1057)

- [ ] **Replace the badge CSS block** (the `/* ── BADGES ──*/` section):

```css
/* ── BADGES ── */
.badge {
  display: inline-flex; align-items: center;
  padding: 3px 9px; border-radius: 3px;
  font-size: 10px; font-weight: 700; letter-spacing: 1px; text-transform: uppercase;
}
.badge-mono  { background: rgba(255,255,255,0.10); color: rgba(255,255,255,0.85); }
.badge-outline { background: transparent; color: rgba(255,255,255,0.7); border: 1px solid rgba(255,255,255,0.25); }
```

- [ ] **Update all badge HTML** — find every `badge-hot`, `badge-info`, `badge-new` in the file and replace:
  - `badge-hot` → `badge-mono`
  - `badge-info` → `badge-outline`
  - `badge-new` → `badge-outline`

  Affected lines (search for `class="badge`):
  - Hero eyebrow (`badge-hot` → `badge-mono`)
  - Dune card poster badge (`badge-hot` → `badge-mono`)
  - Gladiator II card (`badge-info` → `badge-outline`)
  - Wicked card (`badge-new` → `badge-outline`)
  - Detail page IMAX badge (`badge-info` → `badge-outline`)

- [ ] **Verify in browser:** All badges now look like subtle white pills or white outlines. No pink, no blue, no yellow (except stars).

- [ ] **Commit:**
```bash
git add index.html
git commit -m "style: monochrome badge system, remove colored badge variants"
```

---

## Task 3: Remove Orbs, Add Hero Backdrop Element

**Files:** `index.html` (CSS orb block ~lines 200–206, hero HTML ~lines 854–860)

- [ ] **Remove the orb CSS block** — delete these lines entirely:
```css
.orb {
  position: absolute; border-radius: 50%; filter: blur(80px); pointer-events: none;
}
.orb-1 { width: 500px; height: 500px; background: var(--hot);   opacity: .18; top: -100px; right: 15%; }
.orb-2 { width: 400px; height: 400px; background: var(--info);  opacity: .2;  bottom: 5%;  right: 5%;  }
.orb-3 { width: 250px; height: 250px; background: var(--accent); opacity: .08; top: 40%;   right: 38%; }
```

- [ ] **Add hero backdrop CSS** — insert after `.hero { ... }` block (after the hero wrapper styles):
```css
.hero-backdrop {
  position: absolute; inset: 0;
  width: 100%; height: 100%; object-fit: cover;
  opacity: 0; transition: opacity 1.2s ease;
  z-index: 0;
}
.hero-backdrop.loaded { opacity: 1; }
```

- [ ] **Remove the hero-poster-art `::before` and `::after` pseudo-elements** from CSS — find and delete:
```css
.hero-poster-art::before {
  content: '';
  position: absolute; inset: 0;
  background: linear-gradient(160deg, #2d1b69 0%, #11998e 55%, #eb265c 100%);
}
.hero-poster-art::after {
  content: 'DUNE:\A PART TWO';
  white-space: pre;
  position: absolute; bottom: 28px; left: 24px; right: 24px;
  font-size: 28px; font-weight: 900; letter-spacing: -1px;
  line-height: 1; text-transform: uppercase; color: #fff;
  text-shadow: 0 2px 20px rgba(0,0,0,.5);
}
```

- [ ] **Update hero HTML** — replace the current hero section open div block:

Find:
```html
  <div class="hero-canvas"></div>
  <div class="orb orb-1"></div>
  <div class="orb orb-2"></div>
  <div class="orb orb-3"></div>
  <div class="hero-poster-art" id="hero-art"><div class="hero-poster-shine"></div></div>
```

Replace with:
```html
  <img class="hero-backdrop" id="hero-backdrop" src="" alt="">
  <div class="hero-canvas"></div>
  <div class="hero-poster-art" id="hero-art">
    <div class="poster-bg" id="hero-poster-bg" style="background:linear-gradient(160deg,#2d1b69 0%,#11998e 55%,#eb265c 100%)"></div>
    <div class="hero-poster-shine"></div>
  </div>
```

- [ ] **Verify in browser:** No colored orbs. Hero background falls back to the canvas radial gradient (dark purple blob). Hero poster card shows the gradient fallback. Layout intact.

- [ ] **Commit:**
```bash
git add index.html
git commit -m "style: remove orbs, add hero-backdrop img element for TMDB backdrops"
```

---

## Task 4: Restructure TMDB Fetching

**Files:** `index.html` (JS section, ~lines 1359–1403)

- [ ] **Replace the entire TMDB JS block** (from `const TMDB_KEY` through `loadAllPosters()` call):

```js
/* ── TMDB ── */
const TMDB_KEY = '8bc01eb64fefd7e53f1ee77edd23602f';
const tmdbCache = {};

async function fetchTmdb(tmdbId) {
  const id = String(tmdbId);
  if (tmdbCache[id]) return tmdbCache[id];
  try {
    const res = await fetch(`https://api.themoviedb.org/3/movie/${id}?api_key=${TMDB_KEY}&language=de-DE`);
    const data = await res.json();
    tmdbCache[id] = {
      poster:   data.poster_path   ? `https://image.tmdb.org/t/p/w500${data.poster_path}`   : null,
      backdrop: data.backdrop_path ? `https://image.tmdb.org/t/p/w1280${data.backdrop_path}` : null,
    };
  } catch {
    tmdbCache[id] = { poster: null, backdrop: null };
  }
  return tmdbCache[id];
}

function injectImg(el, url, sizeSrc) {
  if (!url) return;
  const src = sizeSrc ? url.replace('w500', sizeSrc) : url;
  const img = document.createElement('img');
  img.className = 'loading';
  img.src = src;
  img.onload = () => img.classList.remove('loading');
  el.appendChild(img);
}

async function loadAllTmdb() {
  // Collect all unique tmdb IDs from DOM
  const els = document.querySelectorAll('[data-tmdb-id]');
  const ids = [...new Set([...els].map(el => el.dataset.tmdbId))];
  await Promise.all(ids.map(id => fetchTmdb(id)));

  // Inject posters into all poster-bg elements
  els.forEach(el => {
    const d = tmdbCache[el.dataset.tmdbId];
    if (d?.poster) injectImg(el, d.poster);
  });

  // Hero poster (larger size)
  const heroData = tmdbCache['693134'];
  if (heroData?.poster) {
    const heroArt = document.getElementById('hero-art');
    const existing = heroArt.querySelector('img');
    if (!existing) injectImg(heroArt, heroData.poster, 'w780');
  }

  // Hero backdrop
  if (heroData?.backdrop) {
    const backdropEl = document.getElementById('hero-backdrop');
    backdropEl.src = heroData.backdrop;
    backdropEl.onload = () => backdropEl.classList.add('loaded');
  }
}

loadAllTmdb();
```

- [ ] **Verify in browser:** All film card posters load from TMDB. Hero shows actual movie backdrop image fading in (Dune desert image). Open DevTools Network tab — confirm requests to `image.tmdb.org`.

- [ ] **Commit:**
```bash
git add index.html
git commit -m "feat: refactor TMDB fetching to return poster+backdrop, load hero backdrop"
```

---

## Task 5: FILMS[] Data Array

**Files:** `index.html` (JS section — add before `loadAllTmdb()` call)

- [ ] **Insert `FILMS` array** directly before `loadAllTmdb()`:

```js
/* ── Film Data ── */
const FILMS = [
  {
    id: 'dune-2',
    title: 'Dune:<br>Part Two',
    titleShort: 'Dune: Part Two',
    tmdbId: 693134,
    rating: 8.5, votes: '52k',
    duration: '2h 46min', fsk: 12,
    badge: 'Top', imax: true, isNew: false,
    genres: ['Sci-Fi', 'Epos', 'Drama'],
    year: 2024,
    desc: 'Paul Atreides vereint sich mit Chani und den Fremen im Rachefeldzug gegen die Verschwörer, die seine Familie zerstörten — und muss zwischen der Liebe seines Lebens und dem Schicksal des Universums wählen.',
    trailerYtId: 'xI6whUDDVuU',
    gradient: 'linear-gradient(150deg,#2d1b69,#11998e)',
    showtimes: {
      standard: [{ time:'14:30', seats:82 },{ time:'17:00', seats:54 },{ time:'20:15', seats:31 },{ time:'22:45', seats:67 }],
      imax:     [{ time:'15:45', seats:12 },{ time:'19:00', seats:8 }],
      ov:       [{ time:'21:30', seats:44 }],
    },
  },
  {
    id: 'gladiator-2',
    title: 'Gladiator II',
    titleShort: 'Gladiator II',
    tmdbId: 558449,
    rating: 7.7, votes: '38k',
    duration: '2h 28min', fsk: 16,
    badge: null, imax: true, isNew: false,
    genres: ['Action', 'Historienfilm', 'Drama'],
    year: 2024,
    desc: 'Jahrzehnte nach dem Tod von Maximus kämpft Lucius, einst ein freier Mann und nun ein Sklave, in der Arena ums Überleben — und enthüllt dabei die dunklen Geheimnisse seiner eigenen Vergangenheit.',
    trailerYtId: 'oS7Sm_JSGZ8',
    gradient: 'linear-gradient(150deg,#3d0000,#c0392b)',
    showtimes: {
      standard: [{ time:'13:15', seats:71 },{ time:'16:30', seats:49 },{ time:'19:45', seats:22 }],
      imax:     [{ time:'14:00', seats:18 },{ time:'20:00', seats:5 }],
      ov:       [],
    },
  },
  {
    id: 'alien-romulus',
    title: 'Alien:<br>Romulus',
    titleShort: 'Alien: Romulus',
    tmdbId: 945961,
    rating: 7.2, votes: '29k',
    duration: '1h 59min', fsk: 16,
    badge: null, imax: false, isNew: false,
    genres: ['Horror', 'Sci-Fi', 'Thriller'],
    year: 2024,
    desc: 'Eine Gruppe junger Weltraum-Kolonisten gerät bei der Erkundung einer verlassenen Raumstation in eine erschreckende Begegnung mit der gefährlichsten Lebensform des Universums.',
    trailerYtId: 'lqBb4pWq3pA',
    gradient: 'linear-gradient(150deg,#0d1b2a,#2c3e50)',
    showtimes: {
      standard: [{ time:'15:00', seats:63 },{ time:'17:45', seats:41 },{ time:'21:00', seats:28 },{ time:'23:15', seats:55 }],
      imax:     [],
      ov:       [{ time:'20:30', seats:37 }],
    },
  },
  {
    id: 'wicked',
    title: 'Wicked',
    titleShort: 'Wicked',
    tmdbId: 402431,
    rating: 7.6, votes: '44k',
    duration: '2h 40min', fsk: 6,
    badge: null, imax: false, isNew: true,
    genres: ['Musical', 'Fantasy', 'Drama'],
    year: 2024,
    desc: 'Elphaba und Glinda, zwei ungleiche Studentinnen in der Welt von Oz, verbinden sich in einer außergewöhnlichen Freundschaft — bevor das Schicksal sie auf entgegengesetzte Seiten eines epischen Konflikts treibt.',
    trailerYtId: '2rHMVBDI4FU',
    gradient: 'linear-gradient(150deg,#134e5e,#71b280)',
    showtimes: {
      standard: [{ time:'14:00', seats:88 },{ time:'17:30', seats:61 },{ time:'20:45', seats:34 }],
      imax:     [],
      ov:       [{ time:'16:00', seats:52 },{ time:'21:15', seats:29 }],
    },
  },
  {
    id: 'deadpool',
    title: 'Deadpool &<br>Wolverine',
    titleShort: 'Deadpool & Wolverine',
    tmdbId: 533535,
    rating: 7.8, votes: '61k',
    duration: '2h 8min', fsk: 16,
    badge: null, imax: false, isNew: false,
    genres: ['Action', 'Komödie', 'Superhelden'],
    year: 2024,
    desc: 'Wade Wilson, besser bekannt als Deadpool, wird in das Marvel Cinematic Universe katapultiert und trifft auf eine sehr mürrische Version von Wolverine — mit explosiven Folgen.',
    trailerYtId: '73_1biulkYk',
    gradient: 'linear-gradient(150deg,#1a1a2e,#4286f4)',
    showtimes: {
      standard: [{ time:'13:45', seats:77 },{ time:'16:15', seats:53 },{ time:'19:30', seats:19 },{ time:'22:00', seats:42 }],
      imax:     [],
      ov:       [],
    },
  },
];

let currentFilmId = 'dune-2';
```

- [ ] **Verify:** Open DevTools console, type `FILMS.length` — should return `5`. Type `FILMS[0].id` — should return `'dune-2'`.

- [ ] **Commit:**
```bash
git add index.html
git commit -m "feat: add FILMS[] data array with 5 films, showtimes, and trailer IDs"
```

---

## Task 6: setFilm() + Dynamic Detail Page

**Files:** `index.html` (JS + detail page HTML)

- [ ] **Add dynamic IDs to detail page HTML** — find the detail page section and add IDs so JS can update them:

Find (in `#page-detail`):
```html
<h1 class="detail-title">Dune:<br>Part Two</h1>
```
Replace with:
```html
<h1 class="detail-title" id="detail-title">Dune:<br>Part Two</h1>
```

Find:
```html
<span class="detail-star">★ 8.5 <sub>(52k)</sub></span>
```
Replace with:
```html
<span class="detail-star" id="detail-star">★ 8.5 <sub>(52k)</sub></span>
```

Find:
```html
<p class="detail-desc">Paul Atreides...</p>
```
Replace with:
```html
<p class="detail-desc" id="detail-desc">Paul Atreides...</p>
```

Find the tags row (the row with `<span class="tag">Sci-Fi</span>` etc):
```html
<div class="detail-row">
  <span class="tag">Sci-Fi</span>
  <span class="tag">Epos</span>
  <span class="tag">Drama</span>
  <span class="tag">2024</span>
</div>
```
Replace with:
```html
<div class="detail-row" id="detail-tags">
  <span class="tag">Sci-Fi</span>
  <span class="tag">Epos</span>
  <span class="tag">Drama</span>
  <span class="tag">2024</span>
</div>
```

Find the showtimes container (the `<div class="showtimes">` block), add an id to the inner groups container:
```html
<h2 class="showtimes-title">Vorstellung wählen</h2>
<div class="date-tabs">
```
Replace with:
```html
<h2 class="showtimes-title">Vorstellung wählen</h2>
<div class="date-tabs" id="detail-date-tabs">
```

And wrap the `st-group` divs in a container:
Find the first `<div class="st-group">` — insert before it:
```html
<div id="detail-showtimes-groups">
```
And after the last `</div>` closing the last `st-group`, add `</div>`.

- [ ] **Add `setFilm()`, `updateDetailPage()`, `buildShowtimes()` to JS** — insert after the `let currentFilmId = 'dune-2';` line:

```js
function buildBadges(f) {
  let b = '';
  if (f.badge)   b += `<span class="badge badge-mono">${f.badge}</span>`;
  if (f.isNew)   b += `<span class="badge badge-outline">Neu</span>`;
  if (f.imax)    b += `<span class="badge badge-outline">IMAX</span>`;
  return b;
}

function buildShowtimes(f) {
  const container = document.getElementById('detail-showtimes-groups');
  if (!container) return;
  const groups = [];

  if (f.showtimes.standard.length) {
    groups.push({ label: 'Standard', key: 'standard', fmt: 'Standard' });
  }
  if (f.showtimes.imax.length) {
    groups.push({ label: 'IMAX', key: 'imax', fmt: 'IMAX' });
  }
  if (f.showtimes.ov.length) {
    groups.push({ label: 'OV (Englisch)', key: 'ov', fmt: '🇬🇧 OV (Englisch)' });
  }

  container.innerHTML = groups.map(g => `
    <div class="st-group">
      <div class="st-format-label">${g.label}</div>
      <div class="st-pills">
        ${f.showtimes[g.key].map(s => `
          <div class="st-pill" onclick="goBooking('${s.time}', '${g.fmt}', ${g.key === 'ov'})">
            <div class="st-time">${s.time}</div>
            <div class="st-avail">${s.seats} frei</div>
            ${g.key !== 'standard' ? `<div class="st-fmt" style="color:${g.key === 'imax' ? 'rgba(255,255,255,0.5)' : 'var(--accent)'}">${g.label}</div>` : ''}
          </div>
        `).join('')}
      </div>
    </div>
  `).join('');
}

function updateDetailPage(f) {
  document.getElementById('detail-title').innerHTML = f.title;
  document.getElementById('detail-star').innerHTML = `★ ${f.rating} <sub>(${f.votes})</sub>`;
  document.getElementById('detail-desc').textContent = f.desc;
  document.getElementById('detail-tags').innerHTML =
    f.genres.map(g => `<span class="tag">${g}</span>`).join('') +
    `<span class="tag">${f.year}</span>`;

  // Detail poster
  const detailPosterBg = document.querySelector('#page-detail .poster-bg');
  detailPosterBg.style.background = f.gradient;
  detailPosterBg.dataset.tmdbId = f.tmdbId;
  detailPosterBg.querySelectorAll('img').forEach(img => img.remove());
  const td = tmdbCache[String(f.tmdbId)];
  if (td?.poster) {
    const img = document.createElement('img');
    img.className = 'loading';
    img.src = td.poster;
    img.onload = () => img.classList.remove('loading');
    detailPosterBg.appendChild(img);
  }

  buildShowtimes(f);
}

function setFilm(filmId) {
  currentFilmId = filmId;
  const f = FILMS.find(f => f.id === filmId);
  if (!f) return;

  // Hero text
  document.querySelector('.hero-title').innerHTML = f.title;
  document.querySelector('.hero-eyebrow').innerHTML =
    `<span>Jetzt im Kino</span>${buildBadges(f)}`;
  document.querySelector('.hero-meta').innerHTML = `
    <span class="star">★ ${f.rating}</span>
    <div class="dot"></div><span>${f.duration}</span>
    <div class="dot"></div><span>FSK ${f.fsk}</span>
    <div class="dot"></div><span>${f.genres[0]} · ${f.genres[1]}</span>`;
  document.querySelector('.hero-desc').textContent = f.desc;

  // Hero poster
  const heroPosterBg = document.getElementById('hero-poster-bg');
  heroPosterBg.style.background = f.gradient;
  heroPosterBg.querySelectorAll('img').forEach(img => img.remove());
  const heroArt = document.getElementById('hero-art');
  heroArt.querySelectorAll('img').forEach(img => img.remove());
  const td = tmdbCache[String(f.tmdbId)];
  if (td?.poster) {
    const img = document.createElement('img');
    img.className = 'loading';
    img.src = td.poster.replace('w500', 'w780');
    img.onload = () => img.classList.remove('loading');
    heroArt.insertBefore(img, heroArt.querySelector('.hero-poster-shine'));
  }

  // Hero backdrop
  const backdropEl = document.getElementById('hero-backdrop');
  backdropEl.classList.remove('loaded');
  backdropEl.src = '';
  if (td?.backdrop) {
    backdropEl.src = td.backdrop;
    backdropEl.onload = () => backdropEl.classList.add('loaded');
  }

  // Trailer + Letterboxd
  const trailerBtn = document.querySelector('.hero-actions .trailer-btn');
  trailerBtn.onclick = () => openTrailer(f.trailerYtId);
  const lbBtn = document.querySelector('.hero-actions a.trailer-btn');
  if (lbBtn) lbBtn.href = `https://letterboxd.com/film/${f.id}/`;

  updateDetailPage(f);
}
```

- [ ] **Update film card HTML** — add `data-film-id` to each card and update onclick:

Dune card:
```html
<div class="film-card" data-film-id="dune-2" onclick="setFilm('dune-2'); go('detail')">
```
Gladiator II card:
```html
<div class="film-card" data-film-id="gladiator-2" onclick="setFilm('gladiator-2'); go('detail')">
```
Alien: Romulus card:
```html
<div class="film-card" data-film-id="alien-romulus" onclick="setFilm('alien-romulus'); go('detail')">
```
Wicked card:
```html
<div class="film-card" data-film-id="wicked" onclick="setFilm('wicked'); go('detail')">
```
Deadpool card:
```html
<div class="film-card" data-film-id="deadpool" onclick="setFilm('deadpool'); go('detail')">
```

- [ ] **Call `updateDetailPage(FILMS[0])` on initial load** — find `loadAllTmdb();` and change to:
```js
loadAllTmdb().then(() => setFilm('dune-2'));
```

- [ ] **Verify:** Click "Gladiator II" card → detail page shows Gladiator II title, Gladiator II poster, Gladiator II showtimes. Click back → click "Wicked" → Wicked data. Hero also updates when navigating back to home... actually hero only updates on `setFilm()` call, not on `go('home')`. That's fine for now — hero shows whichever film was last selected.

- [ ] **Commit:**
```bash
git add index.html
git commit -m "feat: dynamic film routing — setFilm() drives hero + detail page per film"
```

---

## Task 7: Card Visual Polish

**Files:** `index.html` (`.film-card`, `.poster`, `.poster-bg` CSS)

- [ ] **Find `.film-card` CSS** and update hover/background:

Find the `.film-card` rule and add/change:
```css
.film-card {
  cursor: pointer; border-radius: var(--r-lg);
  animation: cardIn .5s var(--spring) both;
  transition: transform .3s var(--spring);
}
.film-card:hover { transform: scale(1.03); }
```

Find `.card-overlay` (the hover play button overlay) and tone down its background:
```css
.card-overlay {
  position: absolute; inset: 0; border-radius: var(--r-lg);
  background: rgba(0,0,0,0.5);
  display: flex; align-items: center; justify-content: center;
  opacity: 0; transition: opacity .2s;
}
.film-card:hover .card-overlay { opacity: 1; }
```

Find `.card-meta` and update:
```css
.card-meta { padding: 12px 4px 0; }
.card-meta-title { font-size: 14px; font-weight: 700; margin-bottom: 6px; }
.card-meta-sub {
  display: flex; gap: 10px; font-size: 12px;
  color: rgba(255,255,255,0.40);
}
```

- [ ] **Verify in browser:** Cards feel cleaner on hover. No glow effects. Stars still yellow.

- [ ] **Commit:**
```bash
git add index.html
git commit -m "style: cleaner card hover — scale only, dim meta text"
```

---

## Task 8: Coming-Soon Strip + Final Cleanup

**Files:** `index.html` (`.coming-genre` CSS, `nav.solid` bg)

- [ ] **Dim coming-soon genre labels** — find `.coming-genre` CSS rule:
```css
.coming-genre { font-size: 12px; color: rgba(255,255,255,0.35); margin-top: 2px; }
```

- [ ] **Slightly deepen nav solid background** — find `nav.solid`:
```css
nav.solid {
  background: rgba(8,8,10,0.92);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-bottom-color: var(--border);
}
```

- [ ] **Verify full flow in browser:**
  1. Homepage loads — backdrop fades in, no orbs, clean badges
  2. Click each of the 5 film cards — correct title/poster/showtimes in detail
  3. Click a showtime — seat map opens correctly
  4. Complete checkout → confirmation screen works

- [ ] **Commit:**
```bash
git add index.html
git commit -m "style: dim coming-soon genres, deepen nav bg — MUBI-dark polish complete"
```

---

## Task 9: Push to GitHub

- [ ] **Push:**
```bash
git push origin main
```

- [ ] **Verify on GitHub** that all commits appear under `main`.
