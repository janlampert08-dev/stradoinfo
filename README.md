# strado.ch

Marketing-Homepage und Rechtstexte für Strado — kuratierte Fahrstrecken für Auto und Motorrad.

Statische Seite ohne Build-Schritt (reines HTML/CSS), damit sie unabhängig von der eigentlichen App (separates Repo/Deploy) läuft.

```
index.html            Startseite
styles.css            Designsystem, gespiegelt aus app/globals.css des App-Repos
wortmarke.svg         Wortmarke, als CSS-Maske in der Fusszeile und ab 640 px in der Kopfzeile
signet.svg            Signet, als CSS-Maske in der Kopfzeile unter 640 px
favicon.svg           Favicon: das Signet auf der Akzentfläche
favicon.ico           dasselbe Bild als PNG-in-ICO (16/32/48 px) für Safari und ältere Browser
apple-touch-icon.png  dasselbe Bild, 180x180, für den iOS-Homebildschirm
og.png                Freigabebild 1200x630 für og:image
robots.txt            Crawler-Regeln + Verweis auf die Sitemap
sitemap.xml           die eine indexierbare Adresse
llms.txt              Maschinenlesbare Kurzbeschreibung für KI-Assistenten (kein Index-Inhalt)
vercel.json           cleanUrls + Weiterleitungen der alten Pfade + Sicherheits-Header
legal/
  index.html          Übersicht /legal
  impressum.html      /legal/impressum
  datenschutz.html    /legal/datenschutz
  agb.html            /legal/agb
```

## Gestaltung

Die Seite verwendet dasselbe Designsystem wie die App, damit der Sprung von
`strado.ch` nach `app.strado.ch` nicht wie ein Wechsel zwischen zwei Produkten
wirkt. Die Tokens am Kopf von `styles.css` sind eine Kopie derjenigen aus
`app/globals.css` im App-Repo (`janlampert08-dev/strado`) — gleiche Namen,
gleiche Werte. **Wer sie dort ändert, sollte hier mitziehen.**

| Was | Übernommen aus |
| --- | --- |
| Farben, Radien, Schatten, Übergänge, Typo-Skala | `app/globals.css` |
| Knöpfe (`.btn-primary`, `.btn-secondary`, Grösse `sm`) | `components/ui/Button.tsx` |
| Flächen (`.card`: Haarlinie, `--radius-lg`, kein Verlauf) | `components/ui/Card.tsx` |
| Kopfzeile (klebend, transluzent, `blur(24px)`) | `components/Header.tsx` |
| Streckendetail in der Hero-Vorschau | `app/strecken/[id]/page.tsx` |
| Höhenprofil darin | `components/ElevationProfile.tsx` |
| Bestenliste darin | `components/RouteLeaderboardPreview.tsx` |
| Akzent-Knopf „Strecke starten“ darin | `components/GefahrenSection.tsx` |
| Symbole (24er-viewBox, Kontur, `stroke-width` 2) | Lucide, wie `lucide-react` in der App |

Eine bewusste Abweichung, in `styles.css` kommentiert: `--text-hero` und
`.btn-lg` gibt es in der App nicht. Ein Hero steht allein auf leerer Fläche
statt in einer App-Oberfläche und trägt eine Stufe mehr.

Die frühere zweite Abweichung — ein im dunklen Schema aufgehelltes
`--color-warning` — ist entfallen: sie trug allein die Entwurfsbanner und die
orange markierten Platzhalter der Rechtstexte, und beide sind seit dem
7. September 2026 weg. Wer ein Warnelement wieder braucht, holt sich den Token
aus `app/globals.css` des App-Repos zurück.

### Hell und Dunkel

Die Seite folgt der Systemvorgabe (`prefers-color-scheme`), wie die App im
Auslieferungszustand. Die App erlaubt zusätzlich eine manuelle Wahl unter
Einstellungen → Darstellung; diese Seite hat keine Einstellungen und lässt es
deshalb bei den zwei Paletten.

Zwei Dinge bleiben bewusst einfarbig dunkel: `og.png`, weil eine Vorschau im
Chat-Fenster das Schema des Betrachters nicht kennt, und `favicon.svg`, dessen
Akzentfläche in beiden Schemata trägt.

Kopf- und Fusszeile folgen dagegen dem Schema: Wortmarke wie Signet liegen als
Maske über einer Fläche in der Textfarbe, eine Datei für hell und dunkel.

## Vorzeigestrecke

Die Streckenkarte im Hero zeigt bei jedem Aufruf eine andere echte Strecke.
Sie kommt live aus der öffentlichen Strecken-API der App:

```
https://app.strado.ch/api/strecken?hoehenprofil=1
```

Der Endpunkt (`app/api/strecken/route.ts` im App-Repo) ist unauthentifiziert
und liefert genau die freigegebenen, nicht privaten Strecken — dieselbe Sicht,
die ein abgemeldeter Besucher in der App bekommt. Es geht kein Zugangsschlüssel
mit, und in dieses Repo gehört auch keiner. `?hoehenprofil=1` hängt jeder
Strecke ihr Höhenprofil an, ohne das sich die Kurve nicht zeichnen liesse; ohne
den Parameter bleibt die Antwort schlank.

**Die Seite zieht damit von selbst nach**: eine neu freigegebene Strecke
erscheint ab dem nächsten Aufruf, eine umbenannte heisst hier anders, eine
zurückgezogene verschwindet. Vorher stand an dieser Stelle eine von Hand
gepflegte Liste, die genau das nicht tat und beim letzten Abgleich vier
Strecken hinterherhinkte.

Zwei Dinge hängen dafür am App-Repo — **wer sie dort ändert, bricht diese
Karte**:

| Was | Wo im App-Repo |
| --- | --- |
| `Access-Control-Allow-Origin` auf der Strecken-API | `lib/apiCors.ts` |
| Der Parameter `?hoehenprofil=1` und die Feldnamen der Antwort | `app/api/strecken/route.ts` |

Ohne den CORS-Header gibt der Browser die Antwort nicht heraus: diese Seite
läuft auf `strado.ch`, die App auf `app.strado.ch`. Der Header steht dort
bewusst ohne `Access-Control-Allow-Credentials`, es gehen also keine Cookies
mit.

Fällt der Abruf aus — kein JavaScript, kein Netz, ein Fehler in der API —,
bleibt die Strecke stehen, die in `index.html` im Markup steht. Sie ist eine
echte Strecke von damals und darf veralten; das ist der Preis dafür, dass die
Karte nie leer ist und nie „lädt…" zeigt.

Die fünf Fahrernamen und Bestzeiten daneben sind und bleiben erfunden: echte
Nutzernamen und Fahrten gehören niemandem, der ihrer Veröffentlichung auf einer
Werbeseite zugestimmt hat. Deshalb wird der Bestenlisten-Endpunkt der API hier
nicht angefasst. Die Zeiten leiten sich aus der von der API geschätzten
Fahrzeit ab (sie rechnet mit den Tempolimits der Strecke): der erste Rang fährt
genau diese Schätzung, die übrigen brauchen länger. Keine gezeigte Zeit setzt
also voraus, dass jemand schneller fährt als erlaubt — worum die Fusszeile
bittet.

## Marke

Die Wortmarke ist „strado" in Familjen Grotesk Bold (SIL Open Font License)
mit -0.03 em Laufweite, in Pfade gewandelt. Sie liegt hier als
`wortmarke.svg` und im App-Repo als Pfaddaten in `lib/marke.ts` — beide
stammen aus derselben Konvertierung. **Wer eine der beiden ändert, muss die
andere mitziehen**, sonst zeigen Info-Seite und App zwei verschiedene Logos.

Die Kontur trägt `fill="currentColor"`, genau wie im App-Repo. Ein `<img>`
kann `currentColor` nicht erben, deshalb bindet `styles.css` die Datei als
CSS-Maske über einer Fläche in der Textfarbe ein (`.wortmarke`) — eine Datei,
beide Farbschemata. Im Markup steht dabei weiterhin der Text „strado": er
liefert den Linknamen für Screenreader und bleibt sichtbar, falls ein Browser
keine Masken beherrscht.

Die Marke wird kleingeschrieben gesetzt, der Fliesstext schreibt „Strado".

### Signet

Neben der Wortmarke steht das Signet: das „o", flach gedrückt zum Rundkurs.
Es kommt **nicht** aus der Schrift, sondern ist gezeichnete Geometrie — zwei
Ellipsen, deren Aussparung etwas höher sitzt als die Aussenkontur. Deshalb ist
das Band oben schmal und unten breit, und deshalb ist es das Zeichen einer
Strecke in der Landschaft und nicht irgendein Wiederhol-Symbol. Es liegt hier
als `signet.svg`, im App-Repo als `SIGNET` in `lib/marke.ts`; die Zusage oben
gilt für beide Marken gleichermassen.

Drei Dinge, an denen man es kaputtmachen kann:

- Es ist **92 : 54**, also rund 1.7-mal so breit wie hoch. Höhe vorgeben, die
  Breite folgen lassen — nie in ein Quadrat zwingen.
- Die Aussparung läuft **gegen** die Aussenkontur. Unter der
  nonzero-Füllregel, nach der Browser, Satori und Canvas füllen, wäre eine
  gleichläufige Punze Fläche statt Loch. Den Pfad also kopieren, nicht in
  einem Grafikprogramm nachzeichnen.
- Auf den Kacheln (`favicon.svg`, `favicon.ico`, `apple-touch-icon.png`) ist
  es nach der **Breite** bemessen: 70 % der Kante, wie `app/icon.tsx` und
  `app/apple-icon.tsx` im App-Repo. Bei 16 px bleibt das Band oben knapp über
  einem Pixel — das ist so gewollt und getestet; wer es kräftiger will, ändert
  es in beiden Repos zugleich, sonst zeigen die zwei Tabs verschiedene Icons.

Die Kacheln sind aus genau dieser Geometrie gerastert (zwei Ellipsen,
Mittelpunkt 46/27 mit 46×27 und 46/23.5 mit 34×14.5, Zeichen `#fafafa` auf
`#3d5afe`). Ein Build-Schritt liegt dafür bewusst nicht im Repo — die Seite
hat keinen, und die Angaben hier reichen, um die drei Dateien neu zu
erzeugen.

## Rechtstexte

Die drei Rechtstexte liegen unter `/legal/…` und sind aus den Entwürfen in
`docs/rechtstexte/` des App-Repos (`janlampert08-dev/strado`) übernommen. Die
App verlinkt sie über `LEGAL_URLS` in `lib/constants.ts` — **wer hier einen
Pfad ändert, muss dort mitziehen.**

Die frühere Seite `nutzungsbedingungen.html` ist in die AGB aufgegangen: die
AGB decken kostenlose Nutzung und Premium-Abo gemeinsam ab, zwei
nebeneinanderstehende Vertragswerke wären ein Widerspruchsrisiko. `vercel.json`
leitet den alten Pfad dauerhaft auf `/legal/agb` um, ebenso die alten Pfade von
Impressum und Datenschutz.

### Stand der Angaben

Die Pflichtangaben sind seit dem 7. September 2026 eingesetzt (Stand-Datum
aller drei Texte): Anbieter ist Jan Lampert, Einzelunternehmen ohne
Handelsregistereintrag, c/o Softsite AG, Leutschenbachstrasse 45, 8050 Zürich;
Kontakt ausschliesslich per E-Mail; Gerichtsstand Zürich (AGB Ziff. 16.4).
Zwei Angaben entfallen getrennt voneinander: Es besteht keine
MWST-Pflicht (Umsatz unter CHF 100'000), also gibt es keine MWST-Nummer; und
das Einzelunternehmen ist weder im Handelsregister eingetragen noch führt es
eine UID. Der frühere Abschnitt „Register und Steuern" im Impressum ist
deshalb gestrichen, nicht leer gelassen. Die Entwurfsbanner und die orange markierten
Platzhalter sind mit demselben Stand entfernt.

Seit demselben Datum beschreiben die AGB das Premium-Abo, wie es verkauft
wird: Eigene Strecken sind eine Premium-Funktion (Ziff. 3.1/3.2), der
Gründerpreis wird nicht mehr angeboten und gilt nur noch für davor
abgeschlossene Abos (Ziff. 4.3). `index.html` wirbt entsprechend nicht mehr
mit „kostenlos", sondern mit Gratis-Einstieg und eigenen Strecken als
Premium-Funktion („Gratis loslegen, eigene Strecken mit Premium" in der
Meta-Beschreibung, sinngemäss in den Hero-Merkmalen und im Schluss-CTA).

## Domains

`strado.ch` ist registriert und ausgeliefert. Massgebend sind die eigenen
Adressen; die vercel.app-Adressen bleiben nur als Deploy-interne Namen
bestehen und gehören in keinen Link und in keinen Rechtstext mehr.

| Was | Erreichbar unter |
| --- | --- |
| Info-Homepage und Rechtstexte (dieses Repo) | `strado.ch`, Rechtstexte unter `/legal/…` |
| App (`janlampert08-dev/strado`) | `app.strado.ch` |

`strado.ch` antwortet mit einer permanenten Weiterleitung (308) auf
`www.strado.ch`, wo die Seite tatsächlich ausgeliefert wird — der Pfad
bleibt dabei erhalten, `strado.ch/legal/agb` landet also auf
`www.strado.ch/legal/agb`. Verlinkt wird trotzdem überall die Apex-Form
`strado.ch`: sie ist die Marke und die Adresse, die in den Rechtstexten
steht. Wer die Weiterleitung sparen will, stellt bei Vercel die
Apex-Domain als primäre Domain ein — dann entfällt der Sprung, ohne dass
ein Link im Repo geändert werden muss.

Bereits umgestellt (Stand dieses Commits):

1. Custom Domains bei Vercel: `strado.ch`/`www.strado.ch` auf `stradoinfo`,
   `app.strado.ch` auf `strado`.
2. In diesem Repo: alle Links „App öffnen“ in Kopf- und Fusszeile jeder
   Seite, der CTA-Block in `index.html` und `og:url`.
3. In den Rechtstexten: die in AGB Ziff. 1.1 genannten Adressen.
4. In `janlampert08-dev/strado`: `LEGAL_BASE_URL_STANDARD` in
   `lib/constants.ts` zeigt jetzt auf `https://strado.ch`, sodass
   `LEGAL_URLS` auch ohne gesetzte `NEXT_PUBLIC_LEGAL_BASE_URL` korrekt
   auflöst.

Offen: `NEXT_PUBLIC_SITE_URL` in der App auf `https://app.strado.ch`
setzen. Sie steuert den Rückkehr-Link aus dem Stripe-Kundenportal. Das ist
eine Vercel-Umgebungsvariable und lässt sich nicht im Repo erledigen.

Die Auflösung ist inzwischen abgesichert (`lib/siteUrl.ts` im App-Repo):
ohne diese Variable greift `VERCEL_PROJECT_PRODUCTION_URL`, die Vercel
selbst setzt, erst danach `http://localhost:3000`. Eine vergessene
Variable schickt zahlende Kundschaft also nicht mehr auf den eigenen
Rechner. Der ausdrückliche Wert bleibt trotzdem der richtige: er hängt
nicht davon ab, welche Domain Vercel gerade als Produktions-Domain führt.

Der Grundsatz von früher gilt unverändert: in den Texten steht nur eine
Adresse, die uns gehört und die antwortet. Eine Adresse, die man nicht
besitzt, kann jederzeit jemand anderem gehören — und sie stünde dann unter
der Überschrift „Impressum".

## Auffindbarkeit

Vier Angaben, die zusammengehoeren — wer eine aendert, prueft die anderen
drei mit.

| Datei | Was sie sagt |
| --- | --- |
| `robots.txt` | Alles erlaubt, plus der Verweis auf die Sitemap |
| `sitemap.xml` | Genau eine Adresse: die Startseite |
| `<link rel="canonical">` in jeder Seite | Unter welcher Adresse diese Seite "richtig" steht |
| `og:url` in `index.html` | Dieselbe Adresse noch einmal, fuer Chat-Vorschauen |

**Alle vier tragen `www.strado.ch`, nicht die Apex-Form.** Das ist kein
Widerspruch zum Abschnitt "Domains" weiter unten, sondern dessen Konsequenz:
`strado.ch` antwortet mit 308 auf `www.strado.ch`, der Inhalt steht dort.
Verlinkt wird im Fliesstext und in den Rechtstexten weiterhin die Apex-Form —
sie ist die Marke. Die vier Angaben oben sind keine Links fuer Menschen,
sondern Maschinenangaben, und eine Maschinenangabe, die selbst erst
weiterleitet, ist ein Signal gegen ein anderes.

Wer bei Vercel die Apex-Domain zur primaeren macht (siehe "Domains"), dreht
alle vier mit — und nur dann.

### Warum das Canonical nicht optional ist

Diese Seite war bis dahin unter **zwei** Hostnamen gleichzeitig mit 200
erreichbar: `www.strado.ch` und die projekteigene `cornice-ch.vercel.app`.
Byte-gleicher Inhalt, kein Canonical, keine `robots.txt` — fuer eine
Suchmaschine zwei konkurrierende Seiten um dieselben Suchbegriffe. Bei einer
Domain ohne Historie ist das die Aufteilung, die man sich am wenigsten
leisten kann. Das Canonical loest das unabhaengig davon, welche
`*.vercel.app`-Adresse das Projekt gerade noch fuehrt: es zeigt immer auf
`www.strado.ch`.

### Der zweite Riegel: `X-Robots-Tag` fuer jede `*.vercel.app`-Adresse

Ein Canonical ist ein **Hinweis**, keine Anweisung. Eine Suchmaschine darf
ihm folgen und muss nicht; eine Adresse, die mit 200 antwortet, kann also
trotzdem im Index landen. Deshalb steht in `vercel.json` eine zweite
Header-Regel, die ausschliesslich auf Hostnamen mit der Endung
`.vercel.app` greift und dort `X-Robots-Tag: noindex` setzt:

```json
{
  "source": "/(.*)",
  "has": [{ "type": "host", "value": { "suf": ".vercel.app" } }],
  "headers": [{ "key": "X-Robots-Tag", "value": "noindex" }]
}
```

Drei Dinge dazu:

- **Die eigenen Domains sind davon nicht erfasst**, und zwar nicht aus
  Sorgfalt, sondern weil sie nicht auf `.vercel.app` enden. Ein
  Suffix-Vergleich kann `www.strado.ch` nicht treffen. Das ist wichtig: eine
  zu weit gefasste Regel an dieser Stelle nimmt die ganze Seite aus dem
  Index, und das faellt erst Wochen spaeter auf.
- **Suffix statt genauem Hostnamen.** `cornice-ch.vercel.app` ist die
  Adresse von heute; ein Projekt kann weitere bekommen. Die Endung deckt
  alle ab, auch kuenftige.
- **Vorschau-Deployments hatten den Header schon**, den setzt Vercel von
  selbst — nachgemessen. Produktiv verknuepfte `*.vercel.app`-Adressen wie
  `cornice-ch` aber **nicht**, und genau die sind das Problem. Die Regel
  macht die Zusage unabhaengig von einer Vercel-Voreinstellung, die sich
  aendern kann.

Wer die Regel anfasst, prueft sie gegen das offizielle Schema
(`https://openapi.vercel.sh/vercel.json`, Abschnitt `headers`): `suf` ist
dort ein zulaessiger Operator fuer `type: "host"`, erfundene Namen wie
`endswith` werden abgewiesen. Der lokale Testserver aus „Die CSP pruefen"
wendet bewusst nur die Regeln **ohne** `has` an — er stellt damit einen
Aufruf unter `www.strado.ch` nach, wo diese Regel gerade nicht greift.

Die vier Seiten unter `/legal/` tragen `<meta name="robots" content="noindex">`
und stehen deshalb **nicht** in der Sitemap. Sie stehen aus demselben Grund
auch nicht als `Disallow` in `robots.txt`: ein Disallow verbietet das
*Abrufen*, nicht das Indexieren — der Crawler faende das `noindex` dann nie
und liesse die von der Startseite verlinkte Adresse ohne Inhalt im Index
stehen. Dieselbe Ueberlegung wie bei `/fahrer` im App-Repo (`app/robots.ts`).

### Strukturierte Daten

`index.html` traegt einen `application/ld+json`-Graphen aus drei Knoten:
`Organization` (die Anbieterin laut Impressum), `WebSite` und
`SoftwareApplication` (die App samt Funktionsliste und den drei Preisen aus
AGB Ziff. 4.1).

Zwei Regeln dafuer:

1. **Keine erfundenen Bewertungen.** Kein `aggregateRating`, kein
   `ratingValue`. Es gibt keine Nutzerbewertungen der App; eine Zahl zu
   setzen, damit Google Sterne zeichnet, ist genau die Sorte strukturierter
   Daten, fuer die Seiten abgestraft werden. Dieselbe Regel gilt im App-Repo
   fuer `TouristTrip` (`app/strecken/[id]/page.tsx`).
2. **Nichts, was nicht auch sichtbar dasteht.** Jede Angabe im Graphen steht
   so auch auf der Seite oder in den Rechtstexten. Aendern sich die Preise in
   der AGB, aendern sie sich hier mit.

### llms.txt

`llms.txt` ist die maschinenlesbare Entsprechung der Seite fuer
KI-Assistenten (ChatGPT, Perplexity, Gemini): was Strado ist, wohin die
oeffentlichen App-Seiten fuehren und wo die Live-Streckenliste steht
(`https://app.strado.ch/llms.txt` im App-Repo, mit tagesaktuellen Namen
und Preisen aus Stripe). Sie ist bewusst **kein** Index-Inhalt — reiner
Text, keine HTML-Seite, kein Sitemap-Eintrag — und braucht deshalb auch
kein Canonical.

Die Preise hier sind statisch und stehen damit an vierter Stelle
wortgleich: AGB Ziff. 4.1, Premium-Abschnitt und JSON-LD in `index.html`
und diese Datei. Wer sie aendert, aendert alle vier; der Kommentar am Kopf
der Datei sagt dasselbe.

## Sicherheits-Header

`vercel.json` setzt sie fuer alle Pfade. Bis dahin lieferte diese Seite
**keinen einzigen** davon aus — die App unter `app.strado.ch` dagegen den
vollen Satz. Eine statische Broschuerenseite ist kein dankbares Ziel, aber
sie traegt die Rechtstexte und den Absprung in die App, und die Header
kosten hier nichts.

| Header | Wert | Wofuer |
| --- | --- | --- |
| `Content-Security-Policy` | siehe unten | Fremde Skripte, Rahmen, Formularziele |
| `X-Frame-Options` | `DENY` | Clickjacking, zusammen mit `frame-ancestors` |
| `X-Content-Type-Options` | `nosniff` | Kein MIME-Raten |
| `Referrer-Policy` | `strict-origin-when-cross-origin` | Kein voller Pfad an fremde Ziele |
| `Permissions-Policy` | Kamera/Mikrofon/Zahlung/Standort/USB aus | Die Seite braucht nichts davon |
| `Strict-Transport-Security` | `max-age=63072000; includeSubDomains` | HTTPS erzwingen |
| `X-Robots-Tag` | `noindex`, **nur** auf `*.vercel.app` | siehe „Der zweite Riegel" oben |

Die CSP ist scharf, nicht `Report-Only`. Dieselbe Begruendung wie im App-Repo
(`lib/csp.ts`): eine Policy ohne `report-uri` beobachtet nichts, sie schickt
jeden Verstoss in die Konsole des Besuchers und sonst nirgendwohin.

Was drin steht und warum:

- `script-src 'self' 'unsafe-inline'` — die Seite traegt vier Inline-Skripte
  (Vercel-Insights-Shim, Vorzeigestrecke, Kopfzeilenhoehe, Jahreszahl).
  `'unsafe-eval'` braucht sie nicht und bekommt es nicht.
- `style-src` und `font-src` — Google Fonts (Inter, IBM Plex Mono).
  Ausdruecklich **ohne** `'unsafe-inline'`: die Seite hat weder `<style>`-
  Bloecke noch `style="…"`-Attribute. Das Skript fuer `--header-h` setzt die
  Variable per CSSOM, und das faellt nicht unter `style-src`.
- `connect-src 'self' https://app.strado.ch` — die Vorzeigestrecke holt
  `/api/strecken?hoehenprofil=1` von der App.
- `img-src 'self' data:` — Signet und Wortmarke kommen per CSS `url()` aus
  dem eigenen Repo.
- `form-action 'none'` und `frame-src 'none'` — die Seite hat kein einziges
  Formular und bindet nichts ein.

`preload` fehlt bei HSTS mit Absicht: die Preload-Liste wird gegen die
Apex-Domain geprueft, und die liefert hier nur die Weiterleitung aus. Der
Token waere eine Behauptung, die dieses Projekt nicht einloesen kann.

### Die CSP pruefen

Sie ist im echten Browser geprueft worden und nicht nur gelesen —
`node_modules` gibt es hier keine, der Ablauf war:

1. Einen lokalen Server starten, der die Header aus `vercel.json` und die
   `cleanUrls`-Regel nachbildet.
2. Alle fuenf Seiten in Chromium laden und auf `Refused to …` in der Konsole
   und auf blockierte Requests achten.
3. **Gegenprobe:** ein Skript von einer nicht freigegebenen Origin
   nachladen. Wird es *nicht* blockiert, ist jedes "keine Verstoesse" aus
   Schritt 2 wertlos, weil die Policy dann gar nicht greift.

Beim letzten Lauf: fuenf Seiten ohne Verstoss, Gegenprobe blockiert.

## Lokal ansehen

`cleanUrls` und die Weiterleitungen kommen von Vercel, nicht von der Datei
selbst — ein einfacher Dateiserver zeigt die Seiten deshalb nur unter ihrem
vollen Namen (`/legal/impressum.html`):

```bash
npx serve .
```

## Deploy

Statisches Projekt auf Vercel (`stradoinfo`), Deploy bei jedem Push auf `main`.
