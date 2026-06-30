# ADR-015: Consulting Architecture Route (`/consulting/architectures/[slug]`) Deferred — Same Root Cause as ADR-014

**Date:** 2026-06-30
**Status:** Accepted
**Related:** ADR-014
**Supersedes:** Partially revises component-inventory §1.5 and TAD §5.1, which specified this route as a 🟡 Stub present at MVP.

## Context

Component-inventory §1.5 specified `app/consulting/architectures/[slug]/page.tsx` as a stub route at MVP — present to avoid 404s, rendering "Coming Soon," with `generateStaticParams()` returning `[]` since `data/consulting.ts`'s `referenceArchitectures` array is empty until Phase 2.

This hit the identical Next.js limitation documented in ADR-014: under `output: 'export'`, an empty array from `generateStaticParams()` produces a build failure (`Page "/consulting/architectures/[slug]" is missing "generateStaticParams()"...`) rather than correctly building zero pages. This is the same upstream bug, not a new issue — see ADR-014 for the full citation list ([vercel/next.js#71862](https://github.com/vercel/next.js/issues/71862) and related).

## Decision

**Defer `app/consulting/architectures/[slug]/page.tsx` until the first reference architecture entry exists in `data/consulting.ts`.** At MVP, `/consulting` renders its existing empty-state message ("Reference architecture pattern library — coming soon") in place of the architecture card grid, which already handles zero entries gracefully (component-inventory §1.4). `components/consulting/ArchitectureCard.tsx` is unaffected and remains implemented for Phase 2, when it will link to real, statically-generated pages.

## Rationale

Same as ADR-014: a placeholder-slug workaround would publish a real, inert page into the static export with no corresponding content. Re-adding the route when the first reference architecture is authored is a content-only change — the page component and `generateStaticParams` pattern don't change.

## Consequences

- **Positive:** No build failure, no phantom architecture pages in the sitemap.
- **Negative:** Minor deviation from component-inventory §1.5's "stub exists at MVP to prevent 404s" intent — moot in practice, since zero architectures means zero inbound links to any architecture slug at MVP.
- **General rule:** See ADR-014 Consequences — this route hits the same constraint. The rule about deferring zero-content dynamic routes under `output: 'export'` applies here identically.

## Alternatives Considered

Identical to ADR-014: placeholder-slug rejected, hosting migration rejected as disproportionate, waiting on upstream fix rejected as blocking.
