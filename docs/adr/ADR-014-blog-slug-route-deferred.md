# ADR-014: Blog Dynamic Route (`/blog/[slug]`) Deferred Until First Post — Next.js Static Export Limitation

**Date:** 2026-06-30
**Status:** Accepted
**Supersedes:** Partially revises component-inventory §1.7 and TAD §5.1, which assumed `/blog/[slug]` could exist pre-launch with zero posts.

## Context

Component-inventory §1.7 and TAD §5.1 specified that `app/blog/[slug]/page.tsx` should exist at MVP launch with zero published posts, using `generateStaticParams()` returning an empty array and `dynamicParams = false`. The rationale was that the route would be fully implemented so first posts could be published without code changes (PRD §3.8, §8).

During implementation, this produced a build failure under `output: 'export'`:

```
Error: Page "/blog/[slug]" is missing "generateStaticParams()" so it cannot be used with "output: export" config.
```

This is not a configuration error. It is a confirmed, long-standing Next.js limitation: when `generateStaticParams()` legitimately returns an empty array under static export, Next.js throws this misleading error instead of correctly building zero pages for the route. The bug has been reported against multiple Next.js major versions (13.x–15.x) and reproduced independently at next@15.5.19 during this build:

- [vercel/next.js#71862](https://github.com/vercel/next.js/issues/71862) — "Build fails when generateStaticParams returns an empty array of params"
- [vercel/next.js#58171](https://github.com/vercel/next.js/issues/58171) — "NextJS export does not recognise generateStaticParams"
- [vercel/next.js#56253](https://github.com/vercel/next.js/issues/56253) — related `dynamicParams`/`output: export` interaction

There is no supported workaround under `output: 'export'` other than ensuring `generateStaticParams()` returns at least one param — which is not possible when zero content exists, short of seeding a placeholder page.

## Decision

**Defer `app/blog/[slug]/page.tsx` until the first blog post is authored.** At MVP, only `app/blog/page.tsx` (index, rendering `<EmptyBlog />` per PRD §3.8) ships. The dynamic route is added back as a content-only change — no component logic changes — when `/content/blog/` contains its first `.mdx` file with `published: true`.

`lib/blog.ts`, `data` typing, and all blog UI components (`PostCard`, `PostGrid`, `PostHeader`, `AuthorBlock`, `EmptyBlog`) are unaffected and remain fully implemented per the original component inventory — only the dynamic route file itself is deferred.

## Rationale

- A placeholder-slug workaround (seeding `generateStaticParams()` with a dummy entry to force a non-empty array) was considered and rejected: it would publish a real, inert page into the sitemap and static output that has to be remembered and removed later — a workaround masquerading as architecture.
- Re-adding the route when the first post exists is low-cost: the page component, data access pattern, and `generateStaticParams` implementation do not change, only the file's presence.
- This keeps `main` deployable (ADR-002, ADR-009 philosophy) rather than shipping a known build failure or a hack around it.

## Consequences

- **Positive:** No build-breaking placeholder route, no phantom sitemap entries, clean reintroduction path when content exists.
- **Negative:** `/blog/[slug]` route does not technically "exist" until first post — a minor deviation from the original "no broken routes" intent in PRD §3.3/§8, though moot in practice since zero posts means zero inbound links to any slug.
- **General rule:** Any dynamic route under `output: 'export'` whose `generateStaticParams()` would return `[]` at launch (zero content) should use this deferred pattern rather than being scaffolded as an empty stub. This applies to both this project and any future project sharing the same static export constraint. Document this rule in the site repo's `CLAUDE.md` Hard Constraints section so it is not rediscovered route-by-route.

## Alternatives Considered

- **Placeholder slug in `generateStaticParams()`** — rejected; produces a real published page with no corresponding content, polluting the static export and sitemap.
- **Switch hosting off GitHub Pages to unblock SSR/ISR for this route** — rejected as disproportionate; this is a single route, and ADR-002/ADR-009 already establish GitHub Pages as correct for this project's scope.
- **Wait for upstream Next.js fix** — rejected as a blocking dependency; no fix timeline exists across the multiple open issues, and `main` should stay deployable in the interim.
