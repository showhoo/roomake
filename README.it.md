# Roomake — Note di build

[English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Deutsch](README.de.md) · [Italiano](README.it.md) · [Español](README.es.md)

**Roomake** ([www.roomake.top](https://www.roomake.top)) è una web app di interior design basata sull'AI: si carica la foto di una stanza, si sceglie un tipo di stanza e fino a quattro stili (oppure si scrive un brief tutto proprio), e si ricevono render prima/dopo. Operata da RooMake Teams.

Questo repository **non** contiene il codice sorgente del prodotto. Documenta come è costruito il sito — l'architettura, l'impostazione multilingua, le decisioni SEO, la disciplina di deployment — e lo fa in sette lingue. L'abbiamo messo nero su bianco in parte per noi stessi, in parte perché continuavamo a rispondere alle stesse domande su come un piccolo team rilascia un prodotto di immagini AI a crediti, con i18n e SEO fatti sul serio.

Se state costruendo qualcosa di simile — generazione di immagini AI, crediti, pagamenti, SEO multi-locale — queste note dovrebbero farvi risparmiare qualche vicolo cieco. Ogni pratica qui descritta è una che gestiamo davvero in produzione, compresi gli errori che ci hanno insegnato il perché.

## Cosa contiene

| Doc | Cosa copre |
|---|---|
| [01 · Panoramica](docs/it/01-overview.md) | Cos'è Roomake, la superficie del prodotto e le due proprietà (.top / .cn) |
| [02 · Stack tecnologico](docs/it/02-tech-stack.md) | Next.js 16, la pipeline di rendering, crediti e pagamenti, il data layer |
| [03 · Architettura i18n](docs/it/03-i18n.md) | Sei locale su una sola codebase, la politica hreflang, i font CJK, le pagine legali localizzate |
| [04 · Playbook SEO](docs/it/04-seo.md) | SEO tecnica, dati strutturati, `llms.txt` e la nostra politica sui crawler AI |
| [05 · Deployment](docs/it/05-deployment.md) | Docker, flag di scope in fase di build, caching e disciplina di rollback |

Ogni documento è disponibile in **English · 简体中文 · 日本語 · 한국어 · Deutsch · Italiano · Español** — si cambia tramite la riga delle lingue in cima a ogni file.

## In breve

| | |
|---|---|
| Prodotto | Redesign AI di stanze: 6 tipi di stanza × 34 preset di stile, combinabili fino a 4 stili oppure un brief in testo libero |
| Frontend | Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS, con priorità ai server components |
| Rendering | Modelli immagine della famiglia Flux tramite API di provider hosted, dietro un sottile gateway interno |
| Pagamenti | Crediti prepagati, un credito per render, rimborso automatico sui render falliti; Creem (merchant of record) a livello internazionale |
| Lingue del prodotto | Inglese + 简体中文 attivi · 日本語 / Deutsch / Français / 한국어 in arrivo a seguire |
| Lingue della documentazione | EN · ZH · JA · KO · DE · IT · ES |
| Deployment | Docker (build standalone) dietro un reverse proxy e una CDN; due deployment regionali indipendenti |

## Link

- Prodotto: [www.roomake.top](https://www.roomake.top) · Cina continentale: [roomake.cn](https://roomake.cn)
- Domande o correzioni su queste note: `support@roomake.top`

## Licenza

Il testo di questo repository è rilasciato con licenza [Creative Commons Attribution 4.0](LICENSE). Traducetelo, adattatelo e riusatelo liberamente — l'attribuzione è gradita, ma non richiesta oltre i termini della licenza.
