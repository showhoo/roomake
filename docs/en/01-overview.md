# 01 · Overview — what Roomake is and why these notes are public

`en` · [简体中文](../zh/01-overview.md) · [日本語](../ja/01-overview.md) · [한국어](../ko/01-overview.md) · [Deutsch](../de/01-overview.md) · [Italiano](../it/01-overview.md) · [Español](../es/01-overview.md)

## The product

Roomake ([www.roomake.top](https://www.roomake.top)) redesigns real rooms from a single photo. The flow fits in one sentence:

> Upload a room photo (JPEG/PNG/WebP, up to 10 MB) → choose one of 6 room types (living room, bedroom, kitchen, bathroom, dining room, home office) → pick up to 4 of 34 style presets, or describe the look you want in your own words → get before/after renders.

There is no 3D model, no floor plan, no furniture catalog. That constraint is deliberate: photo-to-render is the fastest path from "I have a room" to "I can see it differently", and it is the only path that works for renters who cannot measure or model anything.

Pricing follows the same minimalism: prepaid credits, one credit per render, and an automatic refund when a render fails. No credits to expire, no subscription required to try the tool.

## Two properties, one codebase

| | [www.roomake.top](https://www.roomake.top) | [www.roomake.cn](https://www.roomake.cn) |
|---|---|---|
| Audience | Global | Mainland China |
| Languages | English (default) + `/zh`, with 日本語 · Deutsch · Français · 한국어 rolling out | 简体中文 only |
| Payments | International checkout (credit cards, wallets) via a merchant-of-record provider | Localized mainland-China checkout |
| Data | Own database and media storage | Own database and media storage — fully isolated |

Both properties are built from the same codebase. A single build-time setting decides which locales each deployment gets; the Chinese site is not a translated copy served from the same servers, but a separate deployment with its own data. See [03 · i18n architecture](03-i18n.md) and [05 · Deployment](05-deployment.md) for how that works.

## Design language

The site leans editorial rather than app-like: serif typography throughout, generous whitespace, folio-style numbering, and renders presented like plates in an architecture annual. Style names (Cream, Wabi Sabi, Modern Chinese…) are treated as proper nouns and stay in English across every locale — only their poetic sublabels are translated. That is a brand decision, and it also happens to keep the design vocabulary consistent for search.

## Why publish build notes

Three reasons, in order of honesty:

1. **We built on open source.** The project grew out of an open-source Next.js starter for AI image products. Publishing how we extended it is returning the favor.
2. **Written standards hold up better than tribal memory.** Most of what you will read — the hreflang policy, the rollback order, the "record the baseline before you refactor" rule — exists because we got it wrong once. Writing it down is how it stays fixed.
3. **It is honest marketing.** A small team that can explain its architecture precisely is more credible than one that only posts renders. If that credibility turns into users, good.

## What we deliberately do not document

- Server locations, endpoints, ports, and every credential or environment value.
- Rendering-cost internals: which engine serves which tier and at what unit economics.
- Anything from the admin side of the product.

These notes describe the *shape* of the system, not the keys to it.

## Repo map

Five documents × seven languages:

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

Next: [02 · Tech stack](02-tech-stack.md)
