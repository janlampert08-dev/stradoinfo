# cornice.ch

Marketing-Homepage für [Cornice](https://cornice-orcin.vercel.app/) — kuratierte Fahrstrecken für Auto und Motorrad.

Statische Seite ohne Build-Schritt (reines HTML/CSS), damit sie unabhängig von der eigentlichen App (separates Repo/Deploy) läuft.

- `index.html` — Seiteninhalt
- `styles.css` — Styling (nutzt dieselben Design-Tokens wie die App: `#fafafa` / `#131316` / `#3d5afe`, Inter)
- `favicon.svg` — Favicon

## Lokal ansehen

Einfach `index.html` im Browser öffnen, oder mit einem beliebigen statischen Server:

```bash
npx serve .
```

## Deploy

Als statisches Projekt auf Vercel/Netlify/GitHub Pages deploybar, Domain `cornice.ch` als Custom Domain eintragen. Der "App öffnen"-Button verlinkt aktuell auf `https://cornice-orcin.vercel.app/` — bei eigener App-Domain (`cornice.app`) dort anpassen.
