# cornice.ch

Marketing-Homepage für [Cornice](https://cornice-orcin.vercel.app/) — kuratierte Fahrstrecken für Auto und Motorrad.

Statische Seite ohne Build-Schritt (reines HTML/CSS), damit sie unabhängig von der eigentlichen App (separates Repo/Deploy) läuft.

- `index.html` — Seiteninhalt
- `styles.css` — Styling im Dark Mode der App: `#0b0b0d` / `#f2f2f4` / `#6b83ff`, Inter + IBM Plex Mono
- `favicon.svg` — Favicon

## Lokal ansehen

Einfach `index.html` im Browser öffnen, oder mit einem beliebigen statischen Server:

```bash
npx serve .
```

## Deploy

Als statisches Projekt auf Vercel/Netlify/GitHub Pages deploybar, Domain `cornice.ch` als Custom Domain eintragen. Der "App öffnen"-Button verlinkt aktuell auf `https://cornice-orcin.vercel.app/` — bei eigener App-Domain (`cornice.app`) dort anpassen.
