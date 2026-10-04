# Roomake — Build-Notizen

[English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Deutsch](README.de.md) · [Italiano](README.it.md) · [Español](README.es.md)

**Roomake** ([www.roomake.top](https://www.roomake.top)) ist eine KI-Webanwendung für Inneneinrichtung: Ein Foto eines Raums hochladen, einen Zimmertyp und bis zu vier Stile auswählen (oder ein eigenes Briefing formulieren) — und die Vorher/Nachher-Renderings kommen zurück. Betrieben wird das Produkt von RooMake Teams.

Dieses Repository enthält **nicht** den Quellcode des Produkts. Es dokumentiert, wie die Website gebaut ist — die Architektur, das Mehrsprachigkeits-Setup, die SEO-Entscheidungen, die Deployment-Disziplin — und das in sieben Sprachen. Wir haben es niedergeschrieben, teils für uns selbst, teils weil wir immer wieder dieselben Fragen beantwortet haben: Wie ein kleines Team ein Credit-basiertes KI-Bildprodukt mit echtem i18n und echtem SEO ausliefert.

Wenn Sie etwas Ähnliches bauen — KI-Bildgenerierung, Credits, Zahlungen, Multi-Locale-SEO —, sollten diese Notizen Sie vor der einen oder anderen Sackgasse bewahren. Jede hier beschriebene Praxis läuft tatsächlich bei uns in Produktion, einschließlich der Fehler, die uns gezeigt haben, warum.

## Was drin ist

| Dok | Inhalt |
|---|---|
| [01 · Überblick](docs/de/01-overview.md) | Was Roomake ist, die Produktfläche und die beiden Websites (.top / .cn) |
| [02 · Tech-Stack](docs/de/02-tech-stack.md) | Next.js 16, die Rendering-Pipeline, Credits & Zahlungen, die Datenschicht |
| [03 · i18n-Architektur](docs/de/03-i18n.md) | Sechs Locales auf einer Codebasis, hreflang-Richtlinie, CJK-Schriften, lokalisierte Rechtsseiten |
| [04 · SEO-Playbook](docs/de/04-seo.md) | Technisches SEO, strukturierte Daten, `llms.txt` und unsere KI-Crawler-Richtlinie |
| [05 · Deployment](docs/de/05-deployment.md) | Docker, Scope-Flags zur Build-Zeit, Caching und Rollback-Disziplin |

Jedes Dokument ist auf **English · 简体中文 · 日本語 · 한국어 · Deutsch · Italiano · Español** verfügbar — der Wechsel erfolgt über die Sprachzeile am Anfang jeder Datei.

## Eckdaten

| | |
|---|---|
| Produkt | KI-Raumneugestaltung: 6 Zimmertypen × 34 Stilvorlagen, bis zu 4 Stile kombinieren oder ein Briefing als Freitext |
| Frontend | Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS, Server Components zuerst |
| Rendering | Bildmodelle der Flux-Familie über gehostete Provider-APIs, hinter einem schlanken internen Gateway |
| Zahlungen | Prepaid-Credits, ein Credit pro Rendering, automatische Rückerstattung bei fehlgeschlagenen Renderings; international über Creem (merchant of record) |
| Produkt-Locales | English + 简体中文 live · 日本語 / Deutsch / Français / 한국어 folgen als Nächstes |
| Dokument-Sprachen | EN · ZH · JA · KO · DE · IT · ES |
| Deployment | Docker (Standalone-Build) hinter Reverse Proxy und CDN; zwei unabhängige regionale Deployments |

## Links

- Produkt: [www.roomake.top](https://www.roomake.top) · Festlandchina: [roomake.cn](https://roomake.cn)
- Fragen oder Korrekturen zu diesen Notizen: `support@roomake.top`

## Lizenz

Der Text in diesem Repository ist unter [Creative Commons Attribution 4.0](LICENSE) lizenziert. Übersetzen, anpassen und frei wiederverwenden — Namensnennung willkommen, über die Lizenzbedingungen hinaus jedoch nicht erforderlich.
