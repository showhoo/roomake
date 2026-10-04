# 05 · Deployment — Docker, scope flags, caching, rollback

`en` · [简体中文](../zh/05-deployment.md) · [日本語](../ja/05-deployment.md) · [한국어](../ko/05-deployment.md) · [Deutsch](../de/05-deployment.md) · [Italiano](../it/05-deployment.md) · [Español](../es/05-deployment.md)

## Two deployments, one image recipe

Production is two independent deployments of the same codebase:

- **roomake.top** — global locale scope, international checkout, its own database and media storage.
- **roomake.cn** — zh-only scope, mainland-China checkout, fully isolated data.

They share nothing at runtime. A bad release on one cannot touch the other; a data bug cannot cross. The cost is running two pipelines, and the cost is worth it — regional isolation is both a compliance story and a blast-radius story.

## Build-time flags are a contract

Everything that varies between deployments is a build-time flag (locale scope, index on/off, public URLs), inlined into the bundle during `next build`. Three rules keep this safe:

1. **Changing a flag means rebuilding, not restarting.** Runtime env changes silently do nothing for inlined constants — a classic way to "ship" nothing.
2. **The dangerous flag fails closed.** The `NOINDEX` switch defaults to *on*: a release whose env file is missing a line produces a hidden site, never an accidentally public one.
3. **Env keys pass an allowlist, twice** — once in the Dockerfile's `ARG` declarations, once in the compose file's build args. A variable that doesn't pass both gates simply isn't in the build; there is no third path for configuration to sneak in.

Release ritual, in order: `cat` the env file against the checklist → build → deploy → immediately `curl` the live `robots.txt`, sitemap and one canonical URL. Thirty seconds of paranoia prevents the two classic disasters (site accidentally noindexed; site accidentally exposing a preview build).

## Caching topology

- **Static assets** (JS chunks, fonts, images): far-future cache headers at the CDN edge, content-hashed filenames — cache forever, invalidate by never reusing names.
- **HTML**: short TTL at the edge. Marketing pages change rarely, but when they do, the fix should be visible in minutes, not after a global cache expiry. Urgent corrections go through an explicit cache purge, which is a documented, rehearsed action — not a folk remedy.
- **Dynamic surfaces** (the tool, account pages): never cached at the edge; session-safe headers set at the app.

Fonts are self-hosted at build time, so font delivery never depends on a third party's uptime or adds a cross-origin DNS round trip to first paint.

## Golden masters before refactors

The deployment pipeline's most valuable habit is a recording discipline: **before** touching code that renders pages, record the rendered HTML of core pages as golden masters (per locale scope); **after**, require the diff to be empty. "It looks the same to me" is not a regression test. The baseline must be recorded from the *old* code — recording it after the refactor is recording your bug as the standard.

## The release checklist (condensed)

1. Env file audited against the allowlist (both gates: Dockerfile `ARG`, compose args).
2. Golden-master diffs green (all live scopes).
3. Bundle budget check on interactive routes.
4. Deploy → live smoke: `robots.txt` (index state correct), `/sitemap.xml` (locale matrix correct), one canonical URL per locale returns 200 with no trailing-slash 308s.
5. Analytics/heartbeat verified from a clean session.
6. Sitemap resubmitted if routes changed; cache purged if the release was a fix users are waiting on.

## Rollback ladder, in escalating order

1. **Feature-level flags** — turn the feature off, keep the release.
2. **Scope/env flag + rebuild** — revert the deployment to a previous behavior set (e.g., drop a locale).
3. **Proxy-level 301 maps** — before removing any route that a search engine may have indexed, install redirects. Deletions come after redirects, never before.
4. **Previous image** — redeploy the last known-good build; data is separate, so images are swappable.

The standing rule behind the ladder: *a rollback must never turn an indexed URL into a 404.* Redirects absorb equity; 404s donate it to nobody.

## Observability and backups

- Self-hosted cookieless analytics for product metrics; server logs shipped off-box for debugging; uptime checks on `/` and the tool route.
- Backups: database file and user media, daily, to storage that is *not* the serving machine — and restore-tested, because an untested backup is a hope, not a backup.

## What we'd tell a team starting from zero

1. Put the metadata generator and the sitemap/hreflang assertions in **before** launch week — retrofitting SEO plumbing is misery.
2. Make the noindex kill switch exist from day one and default it safe.
3. Keep engines/providers behind a thin internal interface; every AI vendor promises uptime and none promise pricing stability.
4. Two isolated regional deployments beat one clever multi-tenant system at small scale.
5. Write the build down. Future-you, and your teammates, will ship faster for it.

These notes: [README](../../README.md) · [01 · Overview](01-overview.md) · [02 · Tech stack](02-tech-stack.md) · [03 · i18n](03-i18n.md) · [04 · SEO](04-seo.md)
