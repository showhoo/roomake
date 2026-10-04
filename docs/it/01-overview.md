# 01 · Panoramica — cos'è Roomake e perché queste note sono pubbliche

[English](../en/01-overview.md) · [简体中文](../zh/01-overview.md) · [日本語](../ja/01-overview.md) · [한국어](../ko/01-overview.md) · [Deutsch](../de/01-overview.md) · `Italiano` · [Español](../es/01-overview.md)

## Il prodotto

Roomake ([www.roomake.top](https://www.roomake.top)) ridisegna stanze reali a partire da una singola foto. Il flusso sta in una frase:

> Carichi la foto di una stanza (JPEG/PNG/WebP, fino a 10 MB) → scegli uno dei 6 tipi di stanza (soggiorno, camera da letto, cucina, bagno, sala da pranzo, home office) → selezioni fino a 4 dei 34 preset di stile, oppure descrivi con parole tue l'aspetto che vuoi → ricevi i render prima/dopo.

Niente modelli 3D, niente piante, niente cataloghi di mobili. Il vincolo è deliberato: il percorso foto→render è la strada più rapida da «ho una stanza» a «la vedo diversamente», ed è l'unica che funziona per gli inquilini che non possono misurare o modellare niente.

I prezzi seguono lo stesso minimalismo: crediti prepagati, un credito per render, e un rimborso automatico quando un render fallisce. Nessun credito con scadenza, nessun abbonamento richiesto per provare lo strumento.

## Due proprietà, una codebase

| | [www.roomake.top](https://www.roomake.top) | [roomake.cn](https://roomake.cn) |
|---|---|---|
| Pubblico | Globale | Cina continentale |
| Lingue | Inglese (predefinito) + `/zh`, con 日本語 · Deutsch · Français · 한국어 in rollout progressivo | Solo 简体中文 |
| Pagamenti | Checkout internazionale (carte di credito, wallet) tramite un provider merchant of record | Checkout localizzato per la Cina continentale |
| Dati | Database e storage dei media propri | Database e storage dei media propri — completamente isolati |

Entrambe le proprietà sono costruite dalla stessa codebase. Una singola impostazione in fase di build decide quali locale ottiene ogni deployment; il sito cinese non è una copia tradotta servita dagli stessi server, ma un deployment separato con i suoi dati. Vedere [03 · Architettura i18n](03-i18n.md) e [05 · Deployment](05-deployment.md) per come funziona.

## Linguaggio del design

Il sito punta su un taglio editoriale più che da applicazione: tipografia serif ovunque, spazi bianchi generosi, numerazione in stile folio e render presentati come tavole di un annuario di architettura. I nomi degli stili (Cream, Wabi Sabi, Modern Chinese…) sono trattati come nomi propri e restano in inglese in ogni locale — vengono tradotte solo le loro poetiche etichette secondarie. È una scelta di brand, e per coincidenza tiene anche coerente il vocabolario del design agli occhi della ricerca.

## Perché pubblichiamo le note di build

Tre motivi, in ordine di onestà:

1. **Abbiamo costruito su open source.** Il progetto è nato da uno starter open source per Next.js pensato per i prodotti di immagini AI. Pubblicare come lo abbiamo esteso è un modo di ricambiare il favore.
2. **Gli standard scritti reggono meglio della memoria di tribù.** Gran parte di ciò che leggerete — la politica hreflang, l'ordine di rollback, la regola «registra la baseline prima di rifattorizzare» — esiste perché una volta l'abbiamo sbagliata. Scriverlo è il modo in cui resta fermo.
3. **È marketing onesto.** Un piccolo team che sa spiegare la propria architettura con precisione è più credibile di uno che pubblica solo render. Se quella credibilità si trasforma in utenti, tanto meglio.

## Cosa non documentiamo deliberatamente

- Posizioni dei server, endpoint, porte, e ogni credenziale o valore d'ambiente.
- Dettagli interni dei costi di rendering: quale motore serve quale fascia e con quali economie unitarie.
- Qualunque cosa proveniente dal lato amministrativo del prodotto.

Queste note descrivono la *forma* del sistema, non le chiavi per entrarci.

## Mappa del repository

Cinque documenti × sette lingue:

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

Prossimo: [02 · Stack tecnologico](02-tech-stack.md)
