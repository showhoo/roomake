# 05 · Deployment — Docker, Scope-Flags, Caching, Rollback

[English](../en/05-deployment.md) · [简体中文](../zh/05-deployment.md) · [日本語](../ja/05-deployment.md) · [한국어](../ko/05-deployment.md) · `Deutsch` · [Italiano](../it/05-deployment.md) · [Español](../es/05-deployment.md)

## Zwei Deployments, ein Image-Rezept

Die Produktion besteht aus zwei unabhängigen Deployments derselben Codebasis:

- **roomake.top** — globaler Locale-Scope, internationaler Checkout, eigene Datenbank und eigener Medienspeicher.
- **www.roomake.cn** — zh-only-Scope, Checkout für Festlandchina, vollständig isolierte Daten.

Zur Laufzeit teilen sie sich nichts. Ein schlechter Release auf der einen kann die andere nicht anfassen; ein Daten-Bug kann nicht hinüberwechseln. Der Preis sind zwei parallel betriebene Pipelines, und der ist es wert — regionale Isolation ist sowohl eine Compliance- als auch eine Blast-Radius-Geschichte.

## Flags zur Build-Zeit sind ein Vertrag

Alles, was zwischen Deployments variiert, ist ein Flag zur Build-Zeit (Locale-Scope, Index an/aus, öffentliche URLs), das während `next build` ins Bundle inlineiert wird. Drei Regeln halten das sicher:

1. **Ein Flag zu ändern bedeutet Neubauen, nicht Neustarten.** Environment-Änderungen zur Laufzeit bewirken bei inlineierten Konstanten still nichts — ein klassischer Weg, rein gar nichts auszuliefern.
2. **Das gefährliche Flag failt closed.** Der `NOINDEX`-Schalter ist standardmäßig *an*: Ein Release, dessen Env-Datei eine Zeile verpasst, produziert eine versteckte Website — niemals eine versehentlich öffentliche.
3. **Env-Keys passieren eine Allowlist, zweimal** — einmal in den `ARG`-Deklarationen des Dockerfiles, einmal in den Build-Args der Compose-Datei. Eine Variable, die beide Tore nicht passiert, ist schlicht nicht im Build; es gibt keinen dritten Weg, über den sich Konfiguration einschleichen könnte.

Release-Ritual, der Reihe nach: die Env-Datei mit `cat` gegen die Checkliste prüfen → bauen → deployen → sofort die Live-`robots.txt`, die Sitemap und eine Canonical-URL mit `curl` abrufen. Dreißig Sekunden Paranoia verhindern die zwei klassischen Katastrophen (Website versehentlich noindexed; Website legt versehentlich einen Preview-Build offen).

## Caching-Topologie

- **Statische Assets** (JS-Chunks, Fonts, Bilder): Cache-Header mit weit in der Zukunft liegendem Ablauf am CDN-Edge, content-gehashte Dateinamen — für immer cachen, invalidieren, indem Namen nie wiederverwendet werden.
- **HTML**: kurze TTL am Edge. Marketingseiten ändern sich selten, aber wenn doch, sollte der Fix in Minuten sichtbar sein, nicht erst nach dem globalen Cache-Ablauf. Dringende Korrekturen laufen über einen expliziten Cache-Purge — eine dokumentierte, geprobte Aktion, kein Hausmittel.
- **Dynamische Flächen** (das Tool, Kontoseiten): nie am Edge gecacht; session-sichere Header setzt die App.

Fonts werden zur Build-Zeit selbst gehostet, sodass die Font-Auslieferung nie von der Uptime eines Drittanbieters abhängt und für den First Paint kein Cross-Origin-DNS-Roundtrip anfällt.

## Golden Master vor Refactorings

Die wertvollste Gewohnheit der Deployment-Pipeline ist eine Aufzeichnungsdisziplin: **Bevor** Code angefasst wird, der Seiten rendert, wird das gerenderte HTML der Kernseiten als Golden Master aufgezeichnet (pro Locale-Scope); **danach** muss der Diff leer sein. „Sieht für mich gleich aus“ ist kein Regressionstest. Die Baseline muss aus dem *alten* Code aufgezeichnet werden — sie nach dem Refactoring aufzuzeichnen heißt, den eigenen Bug als Standard festzuschreiben.

## Die Release-Checkliste (gekürzt)

1. Env-Datei gegen die Allowlist geprüft (beide Tore: Dockerfile `ARG`, Compose-Args).
2. Golden-Master-Diffs grün (alle Live-Scopes).
3. Bundle-Budget-Check auf interaktiven Routen.
4. Deployment → Live-Smoke-Test: `robots.txt` (Index-Status korrekt), `/sitemap.xml` (Locale-Matrix korrekt), eine Canonical-URL pro Locale liefert 200 ohne Trailing-Slash-308s.
5. Analytics/Heartbeat aus einer sauberen Session verifiziert.
6. Sitemap erneut eingereicht, wenn sich Routen geändert haben; Cache gepurged, wenn der Release ein Fix ist, auf den Nutzer warten.

## Rollback-Leiter, in aufsteigender Ordnung

1. **Flags auf Feature-Ebene** — das Feature ausschalten, den Release behalten.
2. **Scope-/Env-Flag + Rebuild** — das Deployment auf einen früheren Verhaltenssatz zurücksetzen (z. B. ein Locale streichen).
3. **301-Maps auf Proxy-Ebene** — bevor irgendeine Route entfernt wird, die eine Suchmaschine indexiert haben könnte, Weiterleitungen installieren. Löschungen kommen nach Weiterleitungen, nie davor.
4. **Vorheriges Image** — den letzten als gut bekannten Build erneut deployen; die Daten sind separat, Images sind also austauschbar.

Die stehende Regel hinter der Leiter: *Ein Rollback darf eine indexierte URL niemals in einen 404 verwandeln.* Weiterleitungen absorbieren Equity; 404s verschenken es an niemanden.

## Observability und Backups

- Selbst gehostete cookieless Analytics für Produktmetriken; Server-Logs werden zum Debuggen auf eine andere Maschine ausgelagert; Uptime-Checks auf `/` und der Tool-Route.
- Backups: Datenbankdatei und Nutzermedien, täglich, auf einen Speicher, der *nicht* die ausliefernde Maschine ist — und restore-getestet, denn ein ungetestetes Backup ist eine Hoffnung, kein Backup.

## Was wir einem Team sagen würden, das bei null anfängt

1. Den Metadaten-Generator und die Sitemap-/hreflang-Assertionen **vor** der Launch-Woche einbauen — SEO-Infrastruktur nachzurüsten ist die reinste Qual.
2. Den Noindex-Kill-Switch ab Tag eins existieren lassen und seinen Default sicher stellen.
3. Engines/Provider hinter einer schlanken internen Schnittstelle halten; jeder KI-Anbieter verspricht Uptime, keiner verspricht Preisstabilität.
4. Zwei isolierte regionale Deployments schlagen auf kleiner Skala jedes clevere Multi-Tenant-System.
5. Schreiben Sie den Build auf. Ihr zukünftiges Ich und Ihre Teamkollegen liefern deswegen schneller aus.

Diese Notizen: [README](../../README.de.md) · [01 · Überblick](01-overview.md) · [02 · Tech-Stack](02-tech-stack.md) · [03 · i18n](03-i18n.md) · [04 · SEO](04-seo.md)
