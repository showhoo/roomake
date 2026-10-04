# 01 · Überblick — was Roomake ist und warum diese Notizen öffentlich sind

[English](../en/01-overview.md) · [简体中文](../zh/01-overview.md) · [日本語](../ja/01-overview.md) · [한국어](../ko/01-overview.md) · `Deutsch` · [Italiano](../it/01-overview.md) · [Español](../es/01-overview.md)

## Das Produkt

Roomake ([www.roomake.top](https://www.roomake.top)) gestaltet echte Räume anhand eines einzigen Fotos neu. Der Ablauf passt in einen Satz:

> Raumfoto hochladen (JPEG/PNG/WebP, bis 10 MB) → einen von 6 Zimmertypen wählen (Wohnzimmer, Schlafzimmer, Küche, Bad, Esszimmer, Homeoffice) → bis zu 4 von 34 Stilvorlagen auswählen oder den gewünschten Look in eigenen Worten beschreiben → Vorher/Nachher-Renderings erhalten.

Es gibt kein 3D-Modell, keinen Grundriss, keinen Möbelkatalog. Diese Einschränkung ist bewusst gewählt: Foto-zu-Rendering ist der schnellste Weg von „Ich habe einen Raum“ zu „Ich sehe ihn anders“ — und der einzige, der für Mieter funktioniert, die nichts vermessen und nichts modellieren können.

Das Preismodell folgt demselben Minimalismus: Prepaid-Credits, ein Credit pro Rendering und eine automatische Rückerstattung, wenn ein Rendering fehlschlägt. Keine Credits, die verfallen, und kein Abo, um das Tool auszuprobieren.

## Zwei Websites, eine Codebasis

| | [www.roomake.top](https://www.roomake.top) | [roomake.cn](https://roomake.cn) |
|---|---|---|
| Zielgruppe | Weltweit | Festlandchina |
| Sprachen | English (Standard) + `/zh`, mit 日本語 · Deutsch · Français · 한국어 in der Ausrollung | Nur 简体中文 |
| Zahlungen | Internationaler Checkout (Kreditkarten, Wallets) über einen merchant-of-record-Anbieter | Lokalisierter Checkout für Festlandchina |
| Daten | Eigene Datenbank und eigener Medienspeicher | Eigene Datenbank und eigener Medienspeicher — vollständig isoliert |

Beide Websites werden aus derselben Codebasis gebaut. Eine einzige Einstellung zur Build-Zeit entscheidet, welche Locales ein Deployment erhält; die chinesische Website ist keine übersetzte Kopie, die von denselben Servern ausgeliefert wird, sondern ein separates Deployment mit eigenen Daten. Wie das funktioniert, beschreiben [03 · i18n-Architektur](03-i18n.md) und [05 · Deployment](05-deployment.md).

## Designsprache

Die Website schlägt eher den editorialen Weg als den App-Look ein: durchgängig Serifenschrift, großzügiger Weißraum, Nummerierung im Folio-Stil und Renderings, die wie Tafeln in einem Architektur-Jahrbuch präsentiert werden. Stilnamen (Cream, Wabi Sabi, Modern Chinese …) werden als Eigennamen behandelt und bleiben in jedem Locale auf Englisch — nur ihre poetischen Untertitel werden übersetzt. Das ist eine Markenentscheidung, und es hält nebenbei das Design-Vokabular für die Suche konsistent.

## Warum wir Build-Notizen veröffentlichen

Drei Gründe, geordnet nach Ehrlichkeit:

1. **Wir haben auf Open Source gebaut.** Das Projekt entstand aus einem Open-Source-Next.js-Starter für KI-Bildprodukte. Zu veröffentlichen, wie wir ihn erweitert haben, ist unsere Gegenleistung.
2. **Geschriebene Standards halten länger als Stammeswissen.** Das meiste, was Sie hier lesen — die hreflang-Richtlinie, die Rollback-Reihenfolge, die Regel „zuerst die Baseline aufzeichnen, dann refaktorieren“ — existiert, weil wir es einmal falsch gemacht haben. Es aufzuschreiben ist der Weg, es dauerhaft zu fixieren.
3. **Es ist ehrliches Marketing.** Ein kleines Team, das seine Architektur präzise erklären kann, ist glaubwürdiger als eines, das nur Renderings postet. Wenn aus dieser Glaubwürdigkeit Nutzer werden — umso besser.

## Was wir bewusst nicht dokumentieren

- Serverstandorte, Endpunkte, Ports sowie sämtliche Credentials und Umgebungswerte.
- Interna der Renderingkosten: welche Engine welchen Tarif bedient und mit welcher Kalkulation pro Einheit.
- Alles, was zum Admin-Bereich des Produkts gehört.

Diese Notizen beschreiben die *Form* des Systems, nicht die Schlüssel dazu.

## Repo-Karte

Fünf Dokumente × sieben Sprachen:

```
docs/
├── en/  01-overview · 02-tech-stack · 03-i18n · 04-seo · 05-deployment
├── zh/  same five, in Simplified Chinese
├── ja/  same five, in Japanese
├── ko/  same five, in Korean
├── de/  same five, in German
├── it/  same five, in Italian
└── es/  same five, in Spanish
```

Weiter: [02 · Tech-Stack](02-tech-stack.md)
