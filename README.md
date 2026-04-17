# CINEX — Modernes Kino-Buchungssystem (Prototyp)

Ein hochmoderner, interaktiver Prototyp für ein Kino-Buchungssystem. Das Ziel dieses Projekts ist es zu demonstrieren, wie ein "Cinematic Dark Design" und eine reibungslose User Experience (UX) im Vergleich zu traditionellen Kino-Websites aussehen können.

## ✨ Features

- **Cinematic Design:** Immersives Erlebnis mit Glow-Effekten, weichen Animationen (Spring Physics) und Glassmorphism-Elementen.
- **Single Page Feel:** Nahtloser 5-seitiger Buchungs-Flow ohne spürbares Neuladen der Seite:
  1. **Homepage:** Hero-Section, Filmgrid und "Demnächst"-Übersicht.
  2. **Filmdetail-Seite:** Hero-Backdrop, Filminfos und Vorstellungsauswahl.
  3. **Saalplan:** Interaktive Sitzplatzauswahl mit VIP-Reihen und Live-Preisberechnung.
  4. **Checkout:** Modernes Layout für Kontaktdaten, Zahlungsart und Promo-Codes.
  5. **Bestätigung:** Animiertes, digitales Ticket mit perforiertem CSS-Design und QR-Code.
- **Live Filmdaten:** Dynamisches Laden der echten Film-Poster über die TMDB (The Movie Database) API.
- **Dark & Light Mode:** Nahtloser Wechsel des Farbschemas direkt in der Navigation.
- **Progressive Web App (PWA):** Kann als App auf Smartphones und Desktop-Geräten installiert werden (dank `manifest.json` und Service Worker).
- **Barrierefreiheit (a11y):** Vollständige Tastatur-Navigation und ARIA-Attribute für Screenreader integriert.

## 🚀 Tech Stack

Dieses Projekt ist als **reiner Frontend-Prototyp** (Single File Component Style) konzipiert, um einfaches Hosting und schnelle Präsentierbarkeit zu gewährleisten:
- Vanilla HTML5
- Vanilla CSS3 (Custom Properties, Grid, Flexbox, CSS Animations)
- Vanilla JavaScript (ES6+ für Routing, Interaktionen und TMDB Fetching)
- Google Fonts (Lato)
- Keine Build-Tools, keine Frameworks (zero dependencies)

## 🛠️ Lokale Installation & Ausführung

Da es sich um reine Web-Technologien handelt, wird kein komplizierter Build-Prozess benötigt.

1. Repository klonen:
   ```bash
   git clone https://github.com/dein-username/kino-prototype.git
   ```
2. Zur Ausführung gibt es zwei Möglichkeiten:
   - **Einfach:** Die Datei `index.html` direkt im Browser öffnen (Doppelklick).
   - **Empfohlen (für PWA-Features):** Nutze einen lokalen Server (z.B. über die VS Code Extension "Live Server"), da Service Worker nur über `http://localhost` oder `https` funktionieren.

---
*Konzept und Prototyp entwickelt, um eine moderne, reibungslose Alternative zu herkömmlichen Kinoportalen zu zeigen.*
