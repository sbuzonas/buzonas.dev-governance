# ADR-020: Design System Storybook Hosting on GitHub Pages

**Date:** 2026-07-01
**Status:** Accepted

## Context

ADR-019 chose Storybook as the design system's component workshop and documentation environment,
and Storybook builds to a standalone static artifact (§4.3). That artifact needs a hosting target.

The choice is bounded by a few facts: the build is static, so no server runtime is required; the
portfolio site is already hosted on GitHub Pages (ADR-002) and the project's CI/CD is GitHub Actions
(ADR-005); and hosting should stay low in cost and maintenance overhead. GitHub Pages' lack of
per-branch preview/staging environments is a known limitation the project already accepted for the
site (ADR-009).

This decision fixes the hosting *platform* only. It does not decide the brand guide's delivery
*topology* — a single theme-switching instance versus per-brand deployments — which remains open and
is a downstream brand implementation's right. The platform chosen here must not force that topology.

## Decision

- The design system's built Storybook is hosted on **GitHub Pages**, served as the static artifact
  Storybook produces and published by GitHub Actions, consistent with the project's existing CI/CD
  (ADR-005). The specific pipeline is an implementation detail.
- A custom domain (for example, a subdomain) may front it; the specific hostname is an implementation
  detail, not fixed here.
- This is a platform choice only. It accommodates either brand-guide delivery topology decided later
  — per-brand delivery maps to each brand repository's own Pages site; a single combined instance
  maps to one Pages site — and forces neither.

## Rationale

- Storybook emits static assets, which GitHub Pages serves directly with no server runtime, fully
  meeting the standalone-static-artifact requirement (§4.3).
- Using GitHub Pages keeps the design system on the same hosting and CI/CD model as the portfolio
  site (ADR-002, ADR-005), avoiding a new vendor, a new platform to learn, and additional cost.
- It is free and low-friction — a build-and-deploy workflow rather than a third-party service with an
  account and token to manage — keeping maintenance overhead low.
- It leaves the brand-guide delivery topology fully open, since Pages supports both a single site and
  one site per brand repository.

## Consequences

- **Positive:** Zero hosting cost, no new vendor, and a single hosting/CI model shared with the
  portfolio site. Custom-domain capable.
- **Trade-off:** GitHub Pages is static-only, so Storybook-native hosting features — per-PR published
  Storybooks, visual regression, UI review — are not provided. This is acceptable because those
  capabilities were deliberately deferred as out of initial scope (ADR-019); the limitation is the
  same one already accepted for the site (ADR-009). If that capability becomes wanted, a dedicated
  hosting service can be revisited in a new decision.
- **Open:** The brand-guide delivery topology and any custom-domain specifics are not decided here.

## Alternatives Considered

- **Chromatic** (Storybook's first-party hosting service) — rejected for now: its differentiators —
  per-PR published Storybooks, visual regression, UI review — are precisely the capabilities
  deliberately deferred as out of initial scope, so adopting it would add a vendor, a token, and
  free-tier snapshot limits in exchange for capability not yet in use. Worth revisiting if and when
  that capability is wanted.
- **Cloudflare Pages / Netlify / Vercel** — rejected: they offer per-PR preview deploys, genuinely
  useful for a component library, but introduce a hosting platform divergent from the project's
  established GitHub Pages standard for marginal benefit at this stage.
