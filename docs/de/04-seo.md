# 04 · SEO-Playbook — technisches SEO, strukturierte Daten und eine ehrliche KI-Crawler-Richtlinie

[English](../en/04-seo.md) · [简体中文](../zh/04-seo.md) · [日本語](../ja/04-seo.md) · [한국어](../ko/04-seo.md) · `Deutsch` · [Italiano](../it/04-seo.md) · [Español](../es/04-seo.md)

## Ein Metadaten-Generator, null handgeschriebene Metas

Titel, Description, Canonical, Open-Graph-/Twitter-Cards, Robots-Meta und das hreflang-Set jeder Seite kommen aus einem einzigen typisierten Generator. Nichts wird pro Seite von Hand gepflegt — genau deshalb kann Meta-Drift (doppelte Canonicals, fehlendes hreflang, verwahrloste Titel) nicht unbemerkt passieren. Wenn Metadaten es wert sind, vorhanden zu sein, sind sie es wert, generiert zu werden.

## URL- und Locale-Politik für SEO

- **Locale-Unterverzeichnisse** (`/ja`, `/de` …): Die Signale bündeln sich auf einer Domain; Subdomains spalten Autorität, Parameter spalten das Crawling.
- **Keine `Accept-Language`-Auto-Weiterleitungen.** Niemals. Sie füttern den Googlebot mit der falschen Variante und spalten CDN-Caches entlang der User-Agent-Grenzen. Hreflang plus ein sichtbarer Sprachumschalter ist das richtige Paar; die automatische Weiterleitung ist die verlockend falsche Antwort.
- **Keine Cookie-basierte Locale-Erinnerung** für öffentliche Seiten: Der erste Besuch muss für Crawler reproduzierbar sein.
- **`x-default` zeigt auf Englisch** (das Erlebnis in der Standardsprache).

## hreflang: Reziprozität oder gar nichts

Unsere Regel: **hreflang verbindet nur Seiten, die echte 1:1-Äquivalente sind, und jedes Mitglied eines Sets muss zurückverweisen.** Kernseiten (Startseite, Tool, Preise) bilden einen vollständigen, generierten, per Skript verifizierten Cluster.

Lokalisierter redaktioneller Content tritt dem Cluster *nicht* bei: Unsere sprachlichen Guides werden nativ für jeden Markt geschrieben — andere Themen, andere Blickwinkel —, sie sind also keine Äquivalente, und sie so zu markieren hieße, Suchmaschinen mit Umwegen anzulügen. Nicht-äquivalente lokalisierte Seiten bekommen einen sauberen Self-Canonical und keine `alternates`. Wenn die Wahl zwischen unvollständigem hreflang und unehrlichem hreflang steht, wählt man keins von beiden.

Sitemap-Einträge spiegeln dieselbe Politik wider: `xhtml:link`-Alternates nur auf äquivalenten URLs, generiert als Routen × Locales-Matrix, Rechtsseiten als EN-only-Einträge.

## Canonical-Hygiene (ein echter Bug, den wir ausgeliefert haben)

Eine Zeit lang erzeugten Locale-Startseiten Canonicals wie `/ja/` — mit abschließendem Slash —, während die Canonical-URL-Form der Site ohne Slash war. Jedes Canonical und jede `og:url` zeigte auf eine 308-Weiterleitung. Die Google Search Console meldet das als „Page Redirects“, die Verdünnung der Duplikatssignale folgt, und im Browser sieht nichts kaputt aus. Zwei Fixes, inzwischen beide dauerhaft:

1. Abschließende Slashes an der Stelle normalisieren, an der Canonical-URLs gebaut werden (Root `/` ausgenommen).
2. Ein Assertion-Skript, das jede Canonical-/OG-URL auflöst und HTTP 200 verlangt — ein Canonical, der über eine 3xx-Weiterleitung läuft, gilt als Build-Fehler.

## Strukturierte Daten, gezielt ausgewählt

- **Enthalten**: `Organization` (mit Firmennamen), `WebSite`, `SoftwareApplication` — mit `availableLanguage`, generiert aus der tatsächlich aktiven Locale-Liste, nicht hardcodiert. Ändern sich die Locales, ändert sich das Schema mit.
- **Bewusst ausgeschlossen**: `FAQPage`-Rich Results. Google beschränkt FAQ-Rich Results auf bekannte, autoritative Websites; für eine neue Domain bringt das Markup nichts. Schema nach erwartetem Ertrag entscheiden, nicht nach Checkliste.

## Bild-SEO

Inneneinrichtung ist eine visuelle Suchkategorie. Jedes Showcase-Rendering trägt in jedem Locale lokalisierte `alt`-Texte, beschreibende Dateinamen und eine schnelle Auslieferung. Das ist der günstigste Traffic-Kanal mit Zinseffekt, den das Produkt neben der Suche selbst hat.

## KI-Crawler: füttert sie mit Antworten, nicht mit Crawl-Budget

Die `robots.txt` fährt eine dreistufige Richtlinie:

| Agent-Klasse | Erlaubt |
|---|---|
| Suchmaschinen | Öffentliche Seiten; Uploads/Ergebnisse und Kontobereiche gesperrt |
| KI-Assistenten (GPTBot, Claude, PerplexityBot, …) | Nur `/llms.txt` und `/llms-full.txt` |
| Alle anderen | Standardmäßige öffentliche Allow-Liste, crawl-delay 1 |

Die KI-Zeile ist eine Haltung, kein Versehen: Bots, die LLMs bedienen, müssen keine Marketing-Website crawlen — sie brauchen *akkurate, aktuelle Fakten*. Deshalb veröffentlichen wir `llms.txt` (ein kuratierter Index: was Roomake ist, wie das Tool funktioniert, 6 Zimmertypen × 34 Stile, Preismodell, Locales, offizielle Links) und `llms-full.txt` (die erweiterte Version) und verweisen KI-Crawler genau darauf. Wenn ein Assistent die Frage „Was ist Roomake“ beantwortet, wollen wir lieber, dass er unsere eigene Zusammenfassung zitiert, statt eine aus sechs gecachten Seiten zu rekonstruieren. Die Dateien werden aus denselben Locale-Metadaten generiert wie das Schema und können deshalb ebenfalls nicht von der Realität abdriften.

## Keywords: nativ, nicht übersetzt

Direkt übersetzte englische Keywords haben null Suchvolumen. Jeder Markt bekommt eine **native Seed-Liste**, geschrieben dafür, wie Menschen dort tatsächlich suchen, zum Beispiel:

- **ja**: AI インテリア, インテリア AI, リフォーム プレビュー, 室内シミュレーション
- **de**: KI Inneneinrichtung, Wohnraum KI gestalten, Renovierung visualisieren
- **ko**: AI 인테리어, 집 꾸미기 AI, 리모델링 시뮬레이션

Seed-Listen fließen organisch in Titel/Descriptions, in marktpezifische Guide-Themen und in die Locale-Metadaten ein — niemals als Keyword-Stuffing, das Suchmaschinen abwerten und Leser bestrafen.

## Messung und Verifikation

- Google Search Console + Bing Webmaster Tools, je Website; Sitemaps werden beim Release eingereicht.
- **Am gerenderten Output verifizieren.** Head-Checks mit `curl` können von dem abweichen, was Browser und Crawler tatsächlich sehen (injizierte Tags, Hydration-Änderungen, Cache-Schichten). Assertionen laufen gegen das gerenderte DOM/HTML, nicht allein gegen den Quelltext.
- Abgesehen von Rankings sind die Frühindikatoren, die wir beobachten: indexierte Seiten pro Locale, hreflang-Fehler in der GSC, Impressionen in der Bildersuche und Abrufe der llms-Dateien durch KI-Bots.

## Datenschutz schlägt SEO, wo es sein muss

Nutzer-Uploads und generierte Ergebnisse erscheinen nie in Suchergebnissen — in `robots.txt` gesperrt und an der Proxy zusätzlich mit `X-Robots-Tag: noindex` doppelt abgesichert. Das Schlafzimmerfoto eines Nutzers ist kein Content-Marketing. Manche Seiten sind schlicht nicht für den Index bestimmt.

## Soft-Launch: ein Flag, um die gesamte Website zu verstecken

Während der privaten Testphase verwandelt ein einzelnes `NOINDEX`-Flag zur Build-Zeit die `robots.txt` in ein `Disallow: /` für alles. Der Default des Flags ist **sicher** (ein Release, dem seine Environment-Konfiguration fehlt, versteckt die Website, statt sie offenzulegen), und die Umschaltung auf den öffentlichen Index wird verifiziert, indem direkt nach dem Release die Live-`robots.txt` gelesen wird — dieselbe Disziplin wie bei jedem Kill-Switch: fail closed, nach dem Umschalten verifizieren.

Weiter: [05 · Deployment](05-deployment.md)
