# 02 · Stack tecnologico — di cosa è fatto il sito

[English](../en/02-tech-stack.md) · [简体中文](../zh/02-tech-stack.md) · [日本語](../ja/02-tech-stack.md) · [한국어](../ko/02-tech-stack.md) · [Deutsch](../de/02-tech-stack.md) · `Italiano` · [Español](../es/02-tech-stack.md)

## Framework

Next.js 16 con l'App Router, React 19, TypeScript, Tailwind CSS. Lo stato del design tool vive in un unico store Zustand; le animazioni sono Framer Motion usato con parsimonia; le primitive UI arrivano da Radix. Formattazione e linting sono affidati a Biome più ESLint; entrambi devono passare prima che qualunque cosa venga rilasciata.

Due impostazioni contano più del resto dello stack:

- **Prima i server components.** Pagine di marketing, prezzi, pagine legali — tutte renderizzate lato server, con JavaScript client prossimo allo zero. Il bundle client si concentra dove l'interattività lo guadagna: il design tool.
- **`output: 'standalone'`.** Ogni artefatto di produzione è un bundle server autonomo, ed è proprio ciò che rende le immagini Docker piccole e i due deployment regionali identici (vedere [05 · Deployment](05-deployment.md)).

## La pipeline di rendering

Il ciclo centrale, in versione semplificata:

1. **Upload.** Una foto, max 10 MB, solo JPEG/PNG/WebP — validata sul client per l'UX e di nuovo sul server per la verità.
2. **Creazione del task.** Il client invia un tipo di stanza, fino a 4 ID di stile e/o un brief in testo libero (max 500 caratteri) e la foto. Riceve un ID di task e fa polling.
3. **L'assemblaggio del prompt avviene lato server.** Vale la pena sottolinearlo: il client invia *solo gli ID di stile*. I frammenti di testo che trasformano un preset di stile in un prompt per il modello immagine vivono esclusivamente sul server e non entrano mai nel bundle client. Sono il libro delle ricette del prodotto.
4. **Generazione.** Un task engine instrada il lavoro verso modelli immagine della famiglia Flux serviti tramite API di provider hosted, dietro un sottile gateway interno. Il gateway esiste perché i motori possano essere sostituiti o assegnati per feature senza toccare il codice di prodotto — il lock-in del provider è un rischio sui prezzi, quindi viene astratto dal primo giorno.
5. **Consegna.** Le coppie prima/dopo vengono renderizzate nella vista dei risultati; per ogni render viene consumato un credito, e un render fallito si rimborsa da solo — senza bisogno di aprire un ticket di supporto.

Le immagini vengono post-processate con sharp (resize, encode, strip) prima dello storage. Upload e risultati sono privati per default: esclusi dai crawler in `robots.txt` *e* serviti con `X-Robots-Tag: noindex` a livello di proxy, così le foto degli utenti non finiscono mai nella ricerca di immagini.

## Account, crediti, pagamenti

- **Accesso.** Google (One Tap) e Microsoft OAuth, più link via email. Le sessioni sono JWT firmati (JOSE). Nessuna password nostra da far trapelare.
- **Crediti.** Un credito è un'unità di rendering. Saldo, pacchetti e rimborsi vivono in un piccolo strato di contabilità accanto al database; i render falliti si rimborsano automaticamente a livello di task engine.
- **Pagamenti.** Il checkout internazionale passa per **Creem in qualità di merchant of record** — sostiene il carrello fiscale e di compliance sul lato pagamenti, uno scambio sensato per un piccolo team che vende a livello globale. La proprietà cinese usa un checkout separato e localizzato. I gestori dei webhook sono idempotenti e verificati; nulla nel percorso di fatturazione si fida del client.

## Data layer

SQLite, a cui si accede attraverso un sottile data-access layer tipizzato. Con due deployment isolati e un volume di scritture modesto, un database embedded elimina un'intera superficie operativa (nessun server database da patchare, fare backup = copiare un file). Se e quando il volume di scritture dicesse il contrario, il data-access layer è l'unica cosa che dovrà cambiare. Le email transazionali partono via SMTP semplice.

## Analytics e privacy

Le analytics sono **self-hosted e cookieless**. Nessun tracker pubblicitario di terze parti, nessun banner di consenso per i visitatori UE, nessuna rivendita di dati — la decisione sulle analytics è stata presa insieme alla postura GDPR, non dopo. Abbiamo rinunciato alle comodità dei funnel per avere una storia sulla privacy che si può raccontare in una frase, e per questo prodotto quello scambio è quello giusto.

## Controlli automatici che vale la pena rubare

- **Diff HTML golden master.** Prima di qualunque refactoring che tocchi il codice delle pagine, viene registrato l'HTML renderizzato delle pagine principali; dopo il refactoring, il diff deve essere vuoto. È così che l'estrazione dei dizionari per sei locale è stata fatta con zero regressioni visive.
- **Budget sui bundle.** I chunk client del design tool hanno sofferture dimensionali esplicite; i dizionari e le feature che le farebbero sforare vengono divisi o respinti.
- **Assertion SEO come script.** Le voci della sitemap, la reciprocità hreflang, gli URL canonical/OG (incluso «gli URL canonical devono effettivamente restituire 200») vengono controllati da uno script, non a occhio. Vedere [04 · Playbook SEO](04-seo.md).

Prossimo: [03 · Architettura i18n](03-i18n.md)
