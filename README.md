# strado.ch

Marketing-Homepage und Rechtstexte für Strado — kuratierte Fahrstrecken für Auto und Motorrad.

Statische Seite ohne Build-Schritt (reines HTML/CSS), damit sie unabhängig von der eigentlichen App (separates Repo/Deploy) läuft.

```
index.html            Startseite
styles.css            Designsystem, gespiegelt aus app/globals.css des App-Repos
wortmarke.svg         Wortmarke, als CSS-Maske in Kopf- und Fusszeile jeder Seite
favicon.svg           Favicon: das "s" der Wortmarke auf der Akzentfläche
favicon.ico           dasselbe Bild als PNG-in-ICO (16/32/48 px) für Safari und ältere Browser
apple-touch-icon.png  dasselbe Bild, 180x180, für den iOS-Homebildschirm
og.png                Freigabebild 1200x630 für og:image
vercel.json           cleanUrls + Weiterleitungen der alten Pfade
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

## Lokal ansehen

`cleanUrls` und die Weiterleitungen kommen von Vercel, nicht von der Datei
selbst — ein einfacher Dateiserver zeigt die Seiten deshalb nur unter ihrem
vollen Namen (`/legal/impressum.html`):

```bash
npx serve .
```

## Deploy

Statisches Projekt auf Vercel (`stradoinfo`), Deploy bei jedem Push auf `main`.
