# 02 · Tech-Stack — woraus die Website besteht

[English](../en/02-tech-stack.md) · [简体中文](../zh/02-tech-stack.md) · [日本語](../ja/02-tech-stack.md) · [한국어](../ko/02-tech-stack.md) · `Deutsch` · [Italiano](../it/02-tech-stack.md) · [Español](../es/02-tech-stack.md)

## Framework

Next.js 16 mit dem App Router, React 19, TypeScript, Tailwind CSS. Der State im Design-Tool liegt in einem einzigen Zustand-Store; animiert wird mit Framer Motion, sparsam eingesetzt; die UI-Primitives stammen von Radix. Formatierung übernimmt Biome plus ESLint; beides muss bestehen, bevor irgendetwas ausgeliefert wird.

Zwei Einstellungen sind wichtiger als der Rest des Stacks:

- **Server Components zuerst.** Marketingseiten, Preise, Rechtsseiten — alles serverseitig gerendert, nahezu null clientseitiges JavaScript. Das Client-Bundle konzentriert sich dort, wo Interaktivität sich verdient: im Design-Tool.
- **`output: 'standalone'`.** Jedes Produktionsartefakt ist ein in sich geschlossenes Server-Bundle — genau das macht die Docker-Images klein und die beiden regionalen Deployments identisch (siehe [05 · Deployment](05-deployment.md)).

## Die Rendering-Pipeline

Die Kernschleife, vereinfacht:

1. **Upload.** Ein Foto, maximal 10 MB, nur JPEG/PNG/WebP — clientseitig für die UX validiert und serverseitig ein zweites Mal, weil dort die Wahrheit liegt.
2. **Aufgabenerstellung.** Der Client sendet einen Zimmertyp, bis zu 4 Stil-IDs und/oder ein Freitext-Briefing (maximal 500 Zeichen) sowie das Foto. Er erhält eine Task-ID und pollt.
3. **Prompt-Assemblierung passiert serverseitig.** Das ist hervorhebenswert: Der Client sendet *nur Stil-IDs*. Die Textbausteine, die aus einer Stilvorlage einen Bildmodell-Prompt machen, liegen ausschließlich auf dem Server und gelangen nie ins Client-Bundle. Sie sind das Rezeptbuch des Produkts.
4. **Generierung.** Eine Task-Engine routet den Job an Bildmodelle der Flux-Familie, die über gehostete Provider-APIs bereitgestellt werden — hinter einem schlanken internen Gateway. Das Gateway existiert, damit Engines pro Feature getauscht oder zugewiesen werden können, ohne Produktcode anzufassen — Provider-Lock-in ist ein Preisrisiko, also wird es ab Tag eins abstrahiert.
5. **Auslieferung.** Vorher/Nachher-Paare rendern in die Ergebnisansicht; pro Rendering wird ein Credit verbraucht, und ein fehlgeschlagenes Rendering erstattet sich automatisch selbst zurück — ohne Support-Ticket.

Bilder werden vor der Speicherung mit sharp nachbearbeitet (Größe ändern, enkodieren, Metadaten strippen). Uploads und Ergebnisse sind standardmäßig privat: in `robots.txt` von Crawlern ausgeschlossen *und* auf der Proxy-Schicht mit `X-Robots-Tag: noindex` ausgeliefert, sodass Nutzerfotos nie in der Bildersuche landen.

## Konten, Credits, Zahlungen

- **Anmeldung.** Google (One Tap) und Microsoft OAuth, dazu E-Mail-Links. Sessions sind signierte JWTs (JOSE). Keine eigenen Passwörter, die leaken könnten.
- **Credits.** Ein Credit ist eine Einheit Rendering. Guthaben, Pakete und Rückerstattungen liegen in einer kleinen Accounting-Schicht neben der Datenbank; fehlgeschlagene Renderings werden auf Task-Engine-Ebene automatisch erstattet.
- **Zahlungen.** Der internationale Checkout läuft über **Creem als merchant of record** — es trägt die zahlungsseitige Steuer- und Compliance-Last, ein sinnvoller Trade für ein kleines Team, das global verkauft. Die chinesische Website fährt einen separaten, lokalisierten Checkout. Webhook-Handler sind idempotent und verifiziert; nichts im Billing-Pfad vertraut dem Client.

## Datenschicht

SQLite, angesprochen über eine typisierte, schlanke Data-Access-Schicht. Bei zwei isolierten Deployments mit überschaubarem Schreibvolumen nimmt eine eingebettete Datenbank eine komplette Ops-Fläche weg (kein Datenbankserver zu patchen, Backup = eine Datei kopieren). Wenn das Schreibvolumen irgendwann etwas anderes verlangt, ist die Data-Access-Schicht das Einzige, was sich ändern muss. Transaktions-E-Mails gehen über schlichtes SMTP raus.

## Analytics und Datenschutz

Analytics laufen **selbst gehostet und ohne Cookies**. Keine Ad-Tracker von Drittanbietern, kein Consent-Banner für EU-Besucher, kein Weiterverkauf von Daten — die Analytics-Entscheidung wurde gemeinsam mit der DSGVO-Position getroffen, nicht danach. Wir haben auf Funnel-Komfort verzichtet, um eine Datenschutz-Story zu bekommen, die in einem Satz erzählbar ist — und für dieses Produkt ist dieser Trade richtig.

## Automatisierte Checks, die sich kopieren lohnen

- **Golden-Master-HTML-Diffs.** Vor jedem Refactoring, das Seitencode anfasst, wird das gerenderte HTML der Kernseiten aufgezeichnet; danach muss der Diff leer sein. So gelang die Extraktion der Wörterbücher für sechs Locales mit null visuellen Regressionen.
- **Bundle-Budgets.** Die Client-Chunks des Design-Tools haben explizite Größenobergrenzen; Wörterbücher und Features, die sie sprengen würden, werden aufgeteilt oder abgelehnt.
- **SEO-Assertionen als Skript.** Sitemap-Einträge, hreflang-Reziprozität und Canonical-/OG-URLs (einschließlich „Canonical-URLs müssen tatsächlich 200 zurückgeben“) prüft ein Skript, kein müdes Auge. Siehe [04 · SEO-Playbook](04-seo.md).

Weiter: [03 · i18n-Architektur](03-i18n.md)
