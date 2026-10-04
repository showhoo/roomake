# 04 · Playbook SEO — SEO tecnica, dati strutturati e una politica onesta per i crawler AI

[English](../en/04-seo.md) · [简体中文](../zh/04-seo.md) · [日本語](../ja/04-seo.md) · [한국어](../ko/04-seo.md) · [Deutsch](../de/04-seo.md) · `Italiano` · [Español](../es/04-seo.md)

## Un generatore di metadata, zero meta scritte a mano

Titolo, descrizione, canonical, Open Graph/Twitter cards, robots meta e set hreflang di ogni pagina arrivano da un unico generatore tipizzato. Niente è scritto a mano pagina per pagina, ed è esattamente per questo che il drift dei meta — canonical duplicati, hreflang mancanti, titoli che marciscono — non può accadere in silenzio. Se i metadata valgono la pena di esserci, valgono la pena di essere generati.

## Politica URL e locale per la SEO

- **Locale in sottocartella** (`/ja`, `/de`…): i segnali si consolidano su un unico dominio; i sottodomini dividono l'autorevolezza, i parametri dividono la scansione.
- **Niente auto-redirect su `Accept-Language`.** Mai. Forniscono a Googlebot la variante sbagliata e dividono le cache della CDN lungo le linee di UA. Hreflang più un selettore di lingua visibile è la coppia corretta; il redirect automatico è la risposta sbagliata allettante.
- **Niente memoria di locale basata su cookie** per le pagine pubbliche: la prima visita deve essere riproducibile da un crawler.
- **`x-default` punta all'inglese** (l'esperienza nella lingua predefinita).

## hreflang: reciprocità o niente

La regola che applichiamo: **l'hreflang collega solo pagine che sono veri equivalenti 1:1, e ogni membro di un set deve ricambiare.** Le pagine principali (home, tool, prezzi) formano un cluster completo, generato e verificato da script.

I contenuti editoriali localizzati *non* entrano nel cluster: le nostre guide per lingua sono scritte in modo nativo per ogni mercato — temi diversi, angolazioni diverse — quindi non sono equivalenti, e marcarle come tali sarebbe mentire ai motori di ricerca con passaggi in più. Le pagine localizzate non equivalenti ricevono un self-canonical pulito e nessun `alternates`. Se la scelta è tra hreflang incompleto e hreflang disonesto, non scegliete nessuno dei due.

Le voci della sitemap rispecchiano la stessa politica: alternates `xhtml:link` solo sugli URL equivalenti, generati come matrice route × locale, pagine legali come voci solo EN.

## Igiene dei canonical (un bug vero che abbiamo rilasciato)

Per un po' le homepage dei locale hanno prodotto canonical come `/ja/` — con lo slash finale — mentre la forma canonica degli URL del sito era senza slash. Ogni canonical e `og:url` puntava a un redirect 308. Google Search Console lo segnala come «pagine con redirect», segue la diluizione dei segnali duplicati, e in un browser non sembra rotto niente. Due correzioni, ormai entrambe permanenti:

1. Normalizzare gli slash finali nel punto in cui gli URL canonical vengono costruiti (con eccezione della radice `/`).
2. Uno script di assertion che risolve ogni URL canonical/OG ed esige HTTP 200 — un canonical che fa 3 redirect è trattato come fallimento della build.

## Dati strutturati, scelti con criterio

- **Inclusi**: `Organization` (con la ragione sociale), `WebSite`, `SoftwareApplication` — con `availableLanguage` generato dalla lista di locale attiva, non cablato nel codice. Quando i locale cambiano, lo schema cambia con loro.
- **Esclusi deliberatamente**: i rich result `FAQPage`. Google riserva i rich result FAQ ai siti autorevoli e noti; quel markup non rende niente per un dominio nuovo. Scegliere lo schema in base al rendimento atteso, non a una checklist.

## SEO delle immagini

L'interior design è una categoria di query visiva. Ogni render in vetrina porta con sé testo `alt` localizzato in ogni locale, nomi di file descrittivi e consegna veloce. È il canale di traffico composto più economico di cui disponga il prodotto, dopo la ricerca stessa.

## Crawler AI: date loro risposte, non crawl budget

`robots.txt` applica una politica a tre livelli:

| Classe di agente | Consentito |
|---|---|
| Motori di ricerca | Pagine pubbliche; upload/risultati e aree account non consentiti |
| Assistenti AI (GPTBot, Claude, PerplexityBot, …) | `/llms.txt` e `/llms-full.txt` **soltanto** |
| Tutti gli altri | Allow-list pubblica standard, crawl-delay 1 |

La riga AI è una presa di posizione, non una dimenticanza: i bot pensati per gli LLM non hanno bisogno di scansionare un sito di marketing, hanno bisogno di *fatti accurati e aggiornati*. Così pubblichiamo `llms.txt` (un indice curato: cos'è Roomake, come funziona lo strumento, 6 tipi di stanza × 34 stili, modello di prezzi, locale, link ufficiali) e `llms-full.txt` (la versione estesa), e puntiamo i crawler AI esattamente lì. Quando un assistente risponde a «cos'è Roomake», preferiamo che citi il nostro riassunto piuttosto che ricostruirne uno da sei pagine in cache. I file sono generati dagli stessi metadata di locale dello schema, quindi nemmeno loro possono divergere dalla realtà.

## Keyword: native, non tradotte

Le keyword inglesi tradotte direttamente hanno volume di ricerca zero. Ogni mercato riceve una **seed list nativa**, scritta per come la gente cerca davvero lì, per esempio:

- **ja**: AI インテリア, インテリア AI, リフォーム プレビュー, 室内シミュレーション
- **de**: KI Inneneinrichtung, Wohnraum KI gestalten, Renovierung visualisieren
- **ko**: AI 인테리어, 집 꾸미기 AI, 리모델링 시뮬레이션

Le seed list finiscono organicamente in titoli/descrizioni, nei temi delle guide per mercato e nei metadata di locale — mai come keyword stuffing, che i motori di ricerca scontano e i lettori puniscono.

## Misurazione e verifica

- Google Search Console + Bing Webmaster Tools, per proprietà, sitemap inviate a ogni release.
- **Verificate dall'output renderizzato.** I controlli head con `curl` possono discordare da ciò che browser e crawler vedono davvero (tag iniettati, cambiamenti da hydration, layer di cache). Le assertion girano contro il DOM/HTML renderizzato, non contro la sola sorgente.
- A parte il ranking, gli indicatori anticipatori che teniamo d'occhio: pagine indicizzate per locale, errori hreflang nella GSC, impression nella ricerca di immagini, e fetch dei file llms da parte dei bot AI.

## La privacy batte la SEO dove deve

Upload degli utenti e risultati generati non compaiono mai nei risultati di ricerca — non consentiti in `robots.txt` e con doppia copertura `X-Robots-Tag: noindex` al proxy. La foto della camera da letto di un utente non è content marketing. Alcune pagine semplicemente non sono per l'indice.

## Lancio soft: un solo flag per nascondere tutto il sito

Durante i test privati, un singolo flag `NOINDEX` in fase di build trasforma `robots.txt` in `Disallow: /` per tutto. Il flag **fa default sul sicuro** (una release a cui manca la configurazione d'ambiente nasconde il sito invece di esporlo), e il passaggio all'indicizzazione pubblica si verifica leggendo il `robots.txt` live subito dopo la release — la stessa disciplina di qualunque kill switch: fail closed, verifica dopo il cambio.

Prossimo: [05 · Deployment](05-deployment.md)
