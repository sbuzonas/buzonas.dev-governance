# ADR-002: GitHub Pages for Hosting

**Date:** 2026-06-29
**Status:** Accepted

## Context

The portfolio site requires a hosting platform. Cost, simplicity, and the demonstrative value of the hosting choice are all considerations. The domain `buzonas.dev` is already secured and pointed to GitHub Pages.

## Decision

Use **GitHub Pages** for production hosting at `buzonas.dev`.

## Rationale

- Zero cost for public repositories
- Domain already configured and verified
- Tightly integrated with GitHub Actions for deployment
- Appropriate for a statically exported Next.js site
- The hosting choice is unremarkable and intentional — complexity in hosting would distract from the portfolio's content

## Consequences

- **Positive:** Free, zero-ops, integrated with source control and CI/CD
- **Negative:** Static files only; no server runtime, no API routes, no edge functions
- **Constraint:** Requires `output: 'export'` in Next.js config; `trailingSlash: true` for correct path resolution; image optimization disabled (`unoptimized: true`)
- **Constraint:** One custom domain per repository — see ADR-009 for staging implications

## Alternatives Considered

- **Vercel** — strong candidate; free tier generous, native Next.js support, enables staging previews and server features. Deferred to Phase 2 if GitHub Pages limitations become blocking.
- **Cloudflare Pages** — strong candidate; free tier, edge network, supports preview deployments. Deferred to Phase 2.
- **AWS S3 + CloudFront** — over-engineered for this use case; adds cost and operational overhead inconsistent with the portfolio's scope

## Future Migration Path

If staging environment (ADR-009) or server-side features become required, migration to Vercel is the preferred path. The static export architecture is compatible with Vercel — migration requires removing `output: 'export'` and updating the deployment workflow.

---
