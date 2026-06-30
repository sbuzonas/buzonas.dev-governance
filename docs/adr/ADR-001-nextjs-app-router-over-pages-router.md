# ADR-001: Next.js App Router over Pages Router

**Date:** 2026-06-29
**Status:** Accepted

## Context

The portfolio site requires a React-based framework with static export capability for GitHub Pages hosting. Next.js supports two routing paradigms: the legacy Pages Router and the current App Router (introduced in Next.js 13, stable in 14+). A choice between them must be made at project inception — mixing paradigms is unsupported.

## Decision

Use the **Next.js App Router**.

## Rationale

- App Router is the current and forward-supported Next.js architecture; Pages Router is in maintenance mode
- React Server Components (RSC) are native to App Router — MDX content renders on the server with zero client-side JavaScript overhead
- `generateStaticParams` replaces `getStaticPaths` with a cleaner, type-safe API for dynamic route enumeration at build time
- `generateMetadata` provides per-page metadata with full TypeScript support — essential for SEO and OpenGraph
- `next/font` and font optimization work correctly in App Router
- The portfolio will serve as a demonstration of modern Next.js practice — App Router is the correct signal to send

## Consequences

- **Positive:** Modern architecture, full RSC support, better TypeScript integration, future-proof
- **Negative:** App Router has a steeper learning curve than Pages Router; some older tutorials and Stack Overflow answers reference Pages Router patterns
- **Constraint:** `output: 'export'` (static export) disables server-specific App Router features: API routes, middleware, ISR, and Server Actions. All dynamic behavior must occur at build time via `generateStaticParams`.

## Alternatives Considered

- **Pages Router** — rejected; maintenance mode, Pages Router patterns are legacy, poor signal for a portfolio demonstrating engineering currency
- **Remix** — rejected; no native static export for GitHub Pages without additional tooling
- **Astro** — rejected; excellent for static sites but React ecosystem is primary for this project's demonstrative purpose

---
