# 02 · Tech stack — what the site is made of

`en` · [简体中文](../zh/02-tech-stack.md) · [日本語](../ja/02-tech-stack.md) · [한국어](../ko/02-tech-stack.md) · [Deutsch](../de/02-tech-stack.md) · [Italiano](../it/02-tech-stack.md) · [Español](../es/02-tech-stack.md)

## Framework

Next.js 16 with the App Router, React 19, TypeScript, Tailwind CSS. State in the design tool is a single Zustand store; animation is Framer Motion used sparingly; UI primitives come from Radix. Formatting is Biome plus ESLint; both must pass before anything ships.

Two settings matter more than the rest of the stack:

- **Server components first.** Marketing pages, pricing, legal pages — all server-rendered, near-zero client JavaScript. The client bundle is concentrated where interactivity earns it: the design tool.
- **`output: 'standalone'`.** Every production artifact is a self-contained server bundle, which is what makes the Docker images small and the two regional deployments identical (see [05 · Deployment](05-deployment.md)).

## The rendering pipeline

The core loop, simplified:

1. **Upload.** One photo, max 10 MB, JPEG/PNG/WebP only — validated on the client for UX and again on the server for truth.
2. **Task creation.** The client posts a room type, up to 4 style IDs and/or a free-text brief (max 500 chars), and the photo. It receives a task ID and polls.
3. **Prompt assembly happens server-side.** This is worth emphasizing: the client sends *style IDs only*. The text fragments that turn a style preset into an image-model prompt live exclusively on the server and never enter the client bundle. They are the product's recipe book.
4. **Generation.** A task engine routes the job to Flux-family image models served through hosted provider APIs, behind a thin internal gateway. The gateway exists so engines can be swapped or assigned per feature without touching product code — provider lock-in is a pricing risk, so it is abstracted on day one.
5. **Delivery.** Before/after pairs render into the results view; one credit is consumed per render, and a failed render refunds itself automatically — no support ticket required.

Images are post-processed with sharp (resize, encode, strip) before storage. Uploads and results are private by default: excluded from crawlers in `robots.txt` *and* served with `X-Robots-Tag: noindex` at the proxy layer, so user photos never end up in image search.

## Accounts, credits, payments

- **Sign-in.** Google (One Tap) and Microsoft OAuth, plus email links. Sessions are signed JWTs (JOSE). No passwords of our own to leak.
- **Credits.** A credit is a unit of rendering. Balance, packs and refunds live in a small accounting layer next to the database; failed renders refund automatically at the task-engine level.
- **Payments.** International checkout runs through **Creem as merchant of record** — it carries the payment-side tax and compliance burden, which is a sensible trade for a small team selling globally. The Chinese property runs a separate, localized checkout. Webhook handlers are idempotent and verified; nothing in the billing path trusts the client.

## Data layer

SQLite, accessed through a typed thin data-access layer. At two isolated deployments with modest write volume, an embedded database removes an entire ops surface (no database server to patch, back up = copy a file). If and when write volume says otherwise, the data-access layer is the only thing that needs to change. Transactional email goes out over plain SMTP.

## Analytics and privacy

Analytics are **self-hosted and cookieless**. No third-party ad trackers, no consent banner for EU visitors, no data resale — the analytics decision was made jointly with the GDPR posture, not after it. We gave up funnel niceties to get a privacy story we can state in one sentence, and for this product that trade is correct.

## Automated checks worth stealing

- **Golden-master HTML diffs.** Before any refactor that touches page code, the rendered HTML of core pages is recorded; after the refactor, the diff must be empty. This is how a six-locale dictionary extraction was done with zero visual regressions.
- **Bundle budgets.** The design tool's client chunks have explicit size ceilings; dictionaries and features that would blow them get split or rejected.
- **SEO assertions as a script.** Sitemap entries, hreflang reciprocity, canonical/OG URLs (including "canonical URLs must actually return 200") are checked by a script, not by eyeballing. See [04 · SEO playbook](04-seo.md).

Next: [03 · i18n architecture](03-i18n.md)
