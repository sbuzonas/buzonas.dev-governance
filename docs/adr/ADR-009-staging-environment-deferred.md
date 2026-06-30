# ADR-009: Staging Environment Deferred — GitHub Pages Limitation

**Date:** 2026-06-29
**Status:** Accepted

## Context

The PRD specifies a staging environment (`staging.buzonas.dev`) for pre-release visual review. The initial architecture proposed deploying staging from a `gh-pages-staging` branch in the same repository as production.

## Decision

**Defer the staging environment to a future hosting migration phase.** Pre-release validation uses a local production build.

## Rationale

- GitHub Pages enforces one custom domain per repository. `staging.buzonas.dev` cannot be served from the same repository as `buzonas.dev` without a second repository — which violates the repository structure philosophy (ADR-011).
- A second repository solely for staging output is maintenance overhead inconsistent with the project's scope.
- The alternative — Vercel or Cloudflare Pages for staging while keeping GitHub Pages for production — introduces split hosting with two deployment targets, adding complexity without proportional benefit at this stage.
- Pre-release validation via local `npm run build` + `npx serve out` + Lighthouse CI provides sufficient confidence for a solo-developer release cadence.

## Interim Validation Process

Before tagging a production release:
1. `npm run build` — full static export to `/out`
2. `npx serve out` — local preview of production build
3. `npx lhci autorun` — Lighthouse CI against local build
4. Manual review against PRD release checklist

## Future Migration Trigger

Staging environment becomes a priority when any of the following occur:
- Multiple contributors require review before production
- Content contributors without local dev environments need pre-release review
- Server-side features (ISR, API routes) are needed — which also requires leaving GitHub Pages

## Preferred Future Path

Migrate to **Vercel** for both staging (preview deployments per branch/PR) and production. The static export architecture is fully compatible with Vercel; migration removes `output: 'export'` from `next.config.ts` and updates the deployment workflow.

## Alternatives Considered

- **Second repository for staging** — rejected; violates repo philosophy, doubles maintenance surface
- **Cloudflare Pages** — viable alternative to Vercel; preview deployments supported; deferred with staging decision
- **Netlify** — viable; deferred with staging decision

---
