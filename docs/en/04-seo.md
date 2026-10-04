# 04 · SEO playbook — technical SEO, structured data, and an honest AI-crawler policy

`en` · [简体中文](../zh/04-seo.md) · [日本語](../ja/04-seo.md) · [한국어](../ko/04-seo.md) · [Deutsch](../de/04-seo.md) · [Italiano](../it/04-seo.md) · [Español](../es/04-seo.md)

## One metadata generator, zero hand-written meta

Every page's title, description, canonical, Open Graph/Twitter cards, robots meta and hreflang set come from a single typed generator. Nothing is hand-written per page, which is precisely why meta drift — duplicate canonicals, missing hreflang, title rot — cannot happen quietly. If metadata is worth having, it is worth generating.

## URL and locale policy for SEO

- **Subdirectory locales** (`/ja`, `/de`…): signals consolidate on one domain; subdomains split authority, parameters split crawling.
- **No `Accept-Language` auto-redirects.** Ever. They feed Googlebot the wrong variant, and they split CDN caches along UA lines. Hreflang plus a visible language switcher is the correct pair; automatic redirect is the tempting wrong answer.
- **No cookie-based locale memory** for public pages: the first visit must be crawler-reproducible.
- **`x-default` points at English** (the default-language experience).

## hreflang: reciprocity or nothing

The rule we run: **hreflang only connects pages that are true 1:1 equivalents, and every member of a set must reciprocate.** Core pages (home, tool, pricing) form a complete, generated, script-verified cluster.

Localized editorial content does *not* join the cluster: our per-language guides are written natively for each market — different topics, different angles — so they are not equivalents, and marking them as such would be lying to search engines with extra steps. Non-equivalent localized pages get a clean self-canonical and no `alternates`. If the choice is between incomplete hreflang and dishonest hreflang, choose neither.

Sitemap entries mirror the same policy: `xhtml:link` alternates on equivalent URLs only, generated as a routes × locales matrix, legal pages as EN-only entries.

## Canonical hygiene (a real bug we shipped)

For a while, locale homepages produced canonicals like `/ja/` — with a trailing slash — while the canonical URL form of the site was slash-less. Every canonical and `og:url` pointed at a 308 redirect. Google Search Console reports this as "page redirects", duplicate-signal dilution follows, and nothing looks broken in a browser. Two fixes, both now permanent:

1. Normalize trailing slashes at the point where canonical URLs are constructed (root `/` excepted).
2. An assertion script that resolves every canonical/OG URL and requires HTTP 200 — a canonical that 3-redirects is treated as a build failure.

## Structured data, chosen deliberately

- **Included**: `Organization` (with legal name), `WebSite`, `SoftwareApplication` — with `availableLanguage` generated from the live locale list, not hardcoded. When locales change, schema changes with them.
- **Deliberately excluded**: `FAQPage` rich results. Google restricts FAQ rich results to well-known authoritative sites; the markup earns nothing for a new domain. Decide schema by expected yield, not by checklist.

## Image SEO

Interior design is a visual query category. Every showcase render carries localized `alt` text in each locale, descriptive filenames, and fast delivery. This is the cheapest compounding traffic channel the product has after search itself.

## AI crawlers: feed them answers, not crawl budget

`robots.txt` runs a three-layer policy:

| Agent class | Allowed |
|---|---|
| Search engines | Public pages; uploads/results and account areas disallowed |
| AI assistants (GPTBot, Claude, PerplexityBot, …) | `/llms.txt` and `/llms-full.txt` **only** |
| Everyone else | Standard public allow-list, crawl-delay 1 |

The AI line is a position, not an oversight: LLM-facing bots don't need to crawl a marketing site, they need *accurate, current facts*. So we publish `llms.txt` (a curated index: what Roomake is, how the tool works, 6 room types × 34 styles, pricing model, locales, official links) and `llms-full.txt` (the expanded version), and we point AI crawlers at exactly those. When an assistant answers "what is Roomake", we would rather it quote our own summary than reconstruct one from six cached pages. The files are generated from the same locale metadata as the schema, so they cannot drift from reality either.

## Keywords: native, not translated

Directly translated English keywords have zero search volume. Each market gets a **native seed list** written for how people actually search there, for example:

- **ja**: AI インテリア, インテリア AI, リフォーム プレビュー, 室内シミュレーション
- **de**: KI Inneneinrichtung, Wohnraum KI gestalten, Renovierung visualisieren
- **ko**: AI 인테리어, 집 꾸미기 AI, 리모델링 시뮬레이션

Seed lists land in titles/descriptions organically, in per-market guide topics, and in locale metadata — never as keyword stuffing, which search engines discount and readers punish.

## Measurement and verification

- Google Search Console + Bing Webmaster Tools, per property, sitemaps submitted on release.
- **Verify from rendered output.** Head checks with `curl` can disagree with what browsers and crawlers actually see (injected tags, hydration changes, caching layers). Assertions run against rendered DOM/HTML, not source alone.
- Ranking aside, the leading indicators we watch: indexed pages per locale, hreflang errors in GSC, image-search impressions, and llms-file fetches by AI bots.

## Privacy beats SEO where it must

User uploads and generated results never appear in search results — disallowed in `robots.txt` and double-covered with `X-Robots-Tag: noindex` at the proxy. A user's bedroom photo is not content marketing. Some pages are simply not for the index.

## Soft launch: one flag to hide the whole site

During private testing, a single build-time `NOINDEX` flag turns `robots.txt` into `Disallow: /` for everything. The flag **defaults to safe** (a release missing its environment configuration hides the site rather than exposing it), and flipping to public index is verified by reading the live `robots.txt` immediately after release — the same discipline as any kill switch: fail closed, verify after flip.

Next: [05 · Deployment](05-deployment.md)
