# Roomake — Build Notes

[English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Deutsch](README.de.md) · [Italiano](README.it.md) · [Español](README.es.md)

**Roomake** ([www.roomake.top](https://www.roomake.top)) is an AI interior-design web app: upload one photo of a room, pick a room type and up to four styles (or write your own brief), and get before/after renders back. Operated by RooMake Teams.

This repository does **not** contain the product's source code. It documents how the site is built — the architecture, the multilingual setup, the SEO decisions, the deployment discipline — and it does so in seven languages. We wrote it down partly for ourselves, partly because we kept answering the same questions about how a small team ships a credit-based AI image product with real i18n and real SEO.

If you are building something similar — AI image generation, credits, payments, multi-locale SEO — these notes should save you a few dead ends. Every practice described here is one we actually run in production, including the mistakes that taught us why.

## What's inside

| Doc | What it covers |
|---|---|
| [01 · Overview](docs/en/01-overview.md) | What Roomake is, the product surface, and the two properties (.top / .cn) |
| [02 · Tech stack](docs/en/02-tech-stack.md) | Next.js 16, the rendering pipeline, credits & payments, the data layer |
| [03 · i18n architecture](docs/en/03-i18n.md) | Six locales on one codebase, hreflang policy, CJK fonts, localized legal pages |
| [04 · SEO playbook](docs/en/04-seo.md) | Technical SEO, structured data, `llms.txt`, and our AI-crawler policy |
| [05 · Deployment](docs/en/05-deployment.md) | Docker, build-time scope flags, caching, and rollback discipline |

Every document is available in **English · 简体中文 · 日本語 · 한국어 · Deutsch · Italiano · Español** — switch via the language line at the top of each file.

## Quick facts

| | |
|---|---|
| Product | AI room redesign: 6 room types × 34 style presets, mix up to 4 styles or free-text a brief |
| Frontend | Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS, server components first |
| Rendering | Flux-family image models through hosted provider APIs, behind a thin internal gateway |
| Payments | Prepaid credits, one credit per render, automatic refund on failed renders; Creem (merchant of record) internationally |
| Product locales | English + 简体中文 live · 日本語 / Deutsch / Français / 한국어 rolling out next |
| Docs languages | EN · ZH · JA · KO · DE · IT · ES |
| Deployment | Docker (standalone build) behind a reverse proxy and CDN; two independent regional deployments |

## Links

- Product: [www.roomake.top](https://www.roomake.top) · Mainland China: [roomake.cn](https://roomake.cn)
- Questions or corrections about these notes: `support@roomake.top`

## License

The text in this repository is licensed under [Creative Commons Attribution 4.0](LICENSE). Translate, adapt and reuse it freely — attribution appreciated, not required beyond the license terms.
