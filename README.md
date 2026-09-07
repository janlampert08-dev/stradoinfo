# strado.ch

Marketing-Homepage und Rechtstexte für Strado — kuratierte Fahrstrecken für Auto und Motorrad.

Statische Seite ohne Build-Schritt (reines HTML/CSS), damit sie unabhängig von der eigentlichen App (separates Repo/Deploy) läuft.

```
index.html            Startseite
styles.css            Styling im Dark Mode der App: #0b0b0d / #f2f2f4 / #6b83ff, Inter + IBM Plex Mono
wortmarke.svg         Wortmarke, als <img> in Kopf- und Fusszeile jeder Seite
favicon.svg           Favicon: das "s" der Wortmarke auf der Akzentfläche
og.png                Freigabebild 1200x630 für og:image
vercel.json           cleanUrls + Weiterleitungen der alten Pfade
legal/
  index.html          Übersicht /legal
  impressum.html      /legal/impressum
  datenschutz.html    /legal/datenschutz
  agb.html            /legal/agb
```

## Marke

Die Wortmarke ist „strado" in Familjen Grotesk Bold (SIL Open Font License)
mit -0.03 em Laufweite, in Pfade gewandelt. Sie liegt hier als
`wortmarke.svg` und im App-Repo als Pfaddaten in `lib/marke.ts` — beide
stammen aus derselben Konvertierung. **Wer eine der beiden ändert, muss die
andere mitziehen**, sonst zeigen Info-Seite und App zwei verschiedene Logos.

Die Datei ist einfarbig in `#f2f2f4` angelegt, weil diese Seite nur ein
Farbschema hat. Im App-Repo läuft dieselbe Kontur über `currentColor` und
funktioniert dadurch in hell und dunkel.

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

### Was noch fehlt

Die orange markierten Stellen (CSS-Klasse `.todo`) sind **Pflichtangaben**, die
nicht erfunden werden konnten:

| Angabe | Seite |
| --- | --- |
| Firmenname und Rechtsform | Impressum, Datenschutz Ziff. 1, AGB Ziff. 1.2 |
| Strasse, Hausnummer, PLZ, Ort | dieselben Stellen |
| Telefonnummer | Impressum (formal optional, für TWINT empfohlen) |
| Vertretungsberechtigte Person | Impressum |
| UID / Handelsregisternummer | Impressum |
| MWST-Nummer | Impressum — Absatz streichen, solange keine Steuerpflicht besteht |
| Gerichtsstand | AGB Ziff. 16.4 |

Ohne diese Angaben gibt es **keine TWINT-Freischaltung über Stripe** und damit
kein sinnvolles Schweizer Zahlungsmittel für das Premium-Abo. Art. 3 Abs. 1
lit. s UWG verlangt sie ohnehin.

Beide Entwurfsbanner (`.legal-draft`) sind ebenfalls zu entfernen, sobald die
Texte anwaltlich geprüft und die Angaben eingesetzt sind. Vorher im ganzen
Verzeichnis nach `class="todo"` und `legal-draft` suchen.

### Vor dem Premium-Start ebenfalls anpassen

`index.html` bewirbt Strado an drei Stellen als „kostenlos" (Meta-Beschreibung,
Hero-Notiz, Schluss-CTA). Das stimmt, solange kein Abo verkauft wird — mit dem
Verkaufsstart wird daraus eine Freemium-Aussage und muss umformuliert werden.

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
