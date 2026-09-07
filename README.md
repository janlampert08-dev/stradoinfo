# cornice.ch

Marketing-Homepage und Rechtstexte für Cornice — kuratierte Fahrstrecken für Auto und Motorrad.

Statische Seite ohne Build-Schritt (reines HTML/CSS), damit sie unabhängig von der eigentlichen App (separates Repo/Deploy) läuft.

```
index.html            Startseite
styles.css            Styling im Dark Mode der App: #0b0b0d / #f2f2f4 / #6b83ff, Inter + IBM Plex Mono
favicon.svg           Favicon
vercel.json           cleanUrls + Weiterleitungen der alten Pfade
legal/
  index.html          Übersicht /legal
  impressum.html      /legal/impressum
  datenschutz.html    /legal/datenschutz
  agb.html            /legal/agb
```

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

`index.html` bewirbt Cornice an drei Stellen als „kostenlos" (Meta-Beschreibung,
Hero-Notiz, Schluss-CTA). Das stimmt, solange kein Abo verkauft wird — mit dem
Verkaufsstart wird daraus eine Freemium-Aussage und muss umformuliert werden.

## Domains

**Es ist noch keine eigene Domain registriert.** Massgebend sind daher
ausschliesslich die vercel.app-Adressen:

| Was | Heute erreichbar unter |
| --- | --- |
| Info-Homepage und Rechtstexte | `cornice-ch.vercel.app` |
| App | `cornice-orcin.vercel.app` |

Geplant ist die Aufteilung Info-Homepage auf der Hauptdomain, Rechtstexte
unter `/legal/…` und die App auf einer `app.`-Subdomain. Welche Domain das
wird, ist offen.

Was beim Kauf einer Domain anzupassen ist — die Liste ist vollständig, damit
nichts auf eine Adresse zeigt, die noch niemandem gehört:

1. Domain bei Vercel als Custom Domain auf `stradoinfo` (Info-Seite) und auf
   `strado` (App) eintragen.
2. In diesem Repo: die Links „App öffnen" in Kopf- und Fusszeile jeder Seite
   sowie `og:url`/`og:image` in `index.html`.
3. In `janlampert08-dev/strado`: die Umgebungsvariable
   `NEXT_PUBLIC_LEGAL_BASE_URL` auf die neue Basis setzen (ohne sie fällt
   `LEGAL_URLS` auf `cornice-ch.vercel.app` zurück, was weiterhin
   funktioniert) und `NEXT_PUBLIC_SITE_URL` mitziehen.
4. In den Rechtstexten: die in AGB Ziff. 1.1 genannte Adresse.

Bis dahin steht in den Texten keine Wunschdomain: eine Adresse, die man
nicht besitzt, kann jederzeit jemand anderem gehören — und sie stünde dann
unter der Überschrift „Impressum".

## Lokal ansehen

`cleanUrls` und die Weiterleitungen kommen von Vercel, nicht von der Datei
selbst — ein einfacher Dateiserver zeigt die Seiten deshalb nur unter ihrem
vollen Namen (`/legal/impressum.html`):

```bash
npx serve .
```

## Deploy

Statisches Projekt auf Vercel (`stradoinfo`), Deploy bei jedem Push auf `main`.
