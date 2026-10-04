# 05 · Deployment — Docker, flag di scope, caching, rollback

[English](../en/05-deployment.md) · [简体中文](../zh/05-deployment.md) · [日本語](../ja/05-deployment.md) · [한국어](../ko/05-deployment.md) · [Deutsch](../de/05-deployment.md) · `Italiano` · [Español](../es/05-deployment.md)

## Due deployment, una sola ricetta d'immagine

La produzione è due deployment indipendenti della stessa codebase:

- **roomake.top** — scope di locale globale, checkout internazionale, database e storage dei media propri.
- **www.roomake.cn** — scope solo-zh, checkout per la Cina continentale, dati completamente isolati.

In runtime non condividono niente. Una release sbagliata su uno non può toccare l'altro; un bug sui dati non può attraversare il confine. Il costo è gestire due pipeline, e il costo vale la pena — l'isolamento regionale è insieme una storia di compliance e una storia di blast radius.

## I flag in fase di build sono un contratto

Tutto ciò che varia tra i deployment è un flag in fase di build (scope di locale, indice on/off, URL pubblici), inline nel bundle durante `next build`. Tre regole lo tengono al sicuro:

1. **Cambiare un flag significa ricostruire, non riavviare.** Le modifiche alle variabili d'ambiente a runtime, per costanti già inline, non fanno silenziosamente niente — un modo classico per «rilasciare» niente.
2. **Il flag pericoloso fallisce in modo chiuso.** L'interruttore `NOINDEX` fa default su *on*: una release a cui manca una riga nel file env produce un sito nascosto, mai uno accidentalmente pubblico.
3. **Le chiavi d'ambiente passano per un allowlist, due volte** — una volta nelle dichiarazioni `ARG` del Dockerfile, una volta nei build args del file compose. Una variabile che non passa entrambi i cancelletti semplicemente non è nella build; non esiste una terza via per cui la configurazione possa intrufolarsi.

Rito di release, in ordine: `cat` del file env contro la checklist → build → deploy → subito dopo `curl` del `robots.txt` live, della sitemap e di un URL canonical. Trenta secondi di paranoia prevengono i due disastri classici (sito accidentalmente noindexato; sito che espone accidentalmente una build di anteprima).

## Topologia di caching

- **Asset statici** (chunk JS, font, immagini): header di cache far-future al bordo della CDN, nomi di file con hash del contenuto — cache per sempre, invalidazione ottenuta non riusando mai gli stessi nomi.
- **HTML**: TTL breve al bordo. Le pagine di marketing cambiano raramente, ma quando lo fanno la correzione deve essere visibile in pochi minuti, non dopo la scadenza globale della cache. Le correzioni urgenti passano per un purge esplicito della cache, un'azione documentata e provata — non un rimedio popolare.
- **Superfici dinamiche** (il tool, le pagine account): mai cache al bordo; header session-safe impostati dall'app.

I font sono self-hosted in fase di build, così la consegna dei font non dipende mai dall'uptime di terzi né aggiunge un round trip DNS cross-origin al first paint.

## Golden master prima dei refactoring

L'abitudine più preziosa della pipeline di deployment è una disciplina di registrazione: **prima** di toccare codice che renderizza pagine, registrate l'HTML renderizzato delle pagine principali come golden master (per scope di locale); **dopo**, esigete il diff vuoto. «A me sembra uguale» non è un test di regressione. La baseline va registrata dal codice *vecchio* — registrarla dopo il refactoring è registrare il vostro bug come standard.

## La checklist di release (in versione condensata)

1. File env verificato contro l'allowlist (entrambi i cancelletti: `ARG` del Dockerfile, args del compose).
2. Diff golden master verdi (tutti gli scope attivi).
3. Controllo dei budget sui bundle nelle route interattive.
4. Deploy → smoke test live: `robots.txt` (stato dell'indice corretto), `/sitemap.xml` (matrice di locale corretta), un URL canonical per locale restituisce 200 senza 308 da slash finale.
5. Analytics/heartbeat verificati da una sessione pulita.
6. Sitemap reinviata se le route sono cambiate; cache svuotata se la release era una correzione per cui gli utenti stanno aspettando.

## Scala di rollback, in ordine di escalation

1. **Flag a livello di feature** — si spegne la feature, si tiene la release.
2. **Flag di scope/env + rebuild** — si riporta il deployment a un set di comportamenti precedente (per esempio, si elimina un locale).
3. **Mappe di redirect 301 a livello di proxy** — prima di rimuovere qualunque route che un motore di ricerca possa aver indicizzato, si installano i redirect. Le cancellazioni arrivano dopo i redirect, mai prima.
4. **Immagine precedente** — si ridistribuisce l'ultima build nota come buona; i dati sono separati, quindi le immagini sono sostituibili.

La regola ferma dietro la scala: *un rollback non deve mai trasformare un URL indicizzato in un 404.* I redirect assorbono l'equity; i 404 la donano a nessuno.

## Osservabilità e backup

- Analytics self-hosted e cookieless per le metriche di prodotto; log di server spediti fuori macchina per il debugging; check di uptime su `/` e sulla route del tool.
- Backup: file del database e media degli utenti, ogni giorno, su storage che *non* è la macchina che serve — e testati con un restore, perché un backup non testato è una speranza, non un backup.

## Cosa diremmo a un team che parte da zero

1. Mettete il generatore di metadata e le assertion su sitemap/hreflang **prima** della settimana di lancio — adattare a posteriori le fondamenta SEO è una sofferenza.
2. Fate esistere il kill switch noindex dal primo giorno e impostatelo di default sul sicuro.
3. Tenete motori/provider dietro un'interfaccia interna sottile; ogni vendor AI promette uptime e nessuno promette stabilità dei prezzi.
4. Alla piccola scala, due deployment regionali isolati battono un solo ingegnoso sistema multi-tenant.
5. Mettete la build per iscritto. Il voi del futuro, e i vostri colleghi, rilasceranno più in fretta grazie a questo.

Queste note: [README](../../README.it.md) · [01 · Panoramica](01-overview.md) · [02 · Stack tecnologico](02-tech-stack.md) · [03 · i18n](03-i18n.md) · [04 · SEO](04-seo.md)
