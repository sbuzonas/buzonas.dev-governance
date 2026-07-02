# ADR-027: Portfolio Site as Design-System Consumer — Boundary & Migration

**Date:** 2026-07-02
**Status:** Accepted

## Context

The Design System PRD requires theme-agnostic components (§3.4), consumability without modifying component logic (§3.5), and reuse by any project (§2.3). ADR-017 fixed the topology: a brand-neutral core monorepo published as packages, each brand external in its own repository producing a style package and a branding site, with buzonas.dev as brand #1 and components distributed as published packages. ADR-024 fixed the dependency direction as one-way — brands depend on the core; the core never depends on a brand — and had the core ship a brand-neutral reference theme. ADR-025 established the style-delivery menu (scan-first or prebuilt) and explicitly left open whether a consuming site drops its own `globals.css`/Tailwind config. ADR-026 fixed the server/client contract.

Those decisions are internal to the design system. The portfolio site's own accepted governance describes a **self-contained** site: its technical architecture places the shadcn primitives, the shared components, `globals.css`, and `tailwind.config.ts` inside the site repository, and authors the buzonas brand tokens in the site's `globals.css`; its component inventory lists all of this as site-owned. The site has been **scaffolded** on that self-contained basis but not shipped.

Two accepted document sets therefore assign the same components and brand values to different homes. This decision reconciles them: it fixes where the boundary between the design system and the portfolio site falls, and how the already-scaffolded site moves onto the design system without blocking on a full up-front rebuild.

## Decision

1. **The portfolio site is a consumer of the design system, not an owner of its component primitives or brand values.**
   - The brand-neutral core owns the theme-agnostic component primitives (the Radix + Tailwind substrate of ADR-018) and the semantic token contract.
   - buzonas is brand #1 (ADR-017): its brand tokens, its brand-signature visual elements (the Appalachian treeline silhouette, the hex lattice, the hex grid), and a derived style/theme package are brand-owned and external to the neutral core. The brand-signature elements specifically must not live in the neutral core — they are buzonas identity.
   - The portfolio site owns only its composition — routes, page layouts, section components, and content-bound components such as the MDX content wrapper — and consumes the core components package plus the buzonas style package.

2. **Partition of the currently-scaffolded pieces.**
   - **Core:** the shadcn/Radix primitive components.
   - **buzonas brand:** the brand tokens (today in the site's `globals.css`/Tailwind config) and the brand-signature SVG elements.
   - **Site-local:** routes and page layouts, the home-page section components, the project/blog/consulting components, the layout chrome (nav, footer, theme toggle), and the MDX content wrapper.
   - **Deferred:** a small set of low-complexity shared primitives — a mono "tech tag" chip, a section "eyebrow" overline, and a status badge — are **not** classified here. It is not yet clear they are worth decomposing into reusable elements, nor whether they are generic rather than brand-specific. They remain site-local until their reuse value is clearer, at which point a generic base may move to the core while any portfolio-specific vocabulary (e.g. the project-status set) stays site-local.

3. **Migration path.** The component primitives are developed inside the core's design harness, rendered against the **real** buzonas brand styles supplied locally, iterated until stable; only then is the buzonas style/theme package derived from the stabilized values, and the scaffolded self-contained site is migrated onto the published packages. The current self-contained scaffold is explicitly interim, not the target.

4. **The mechanism that supplies the real buzonas brand styles to the core harness during that development — without the core acquiring any dependency on the brand — is not decided here.** It is the §3.6 decoupled-brand-theme-authoring capability, resolved separately, and deliberately kept out of this record.

## Rationale

- Ratifying the site-as-consumer boundary makes ADR-017/024's topology actually reach the portfolio instead of the portfolio silently contradicting it; it resolves the one place the two document sets disagree.
- Keeping the brand-signature elements and brand tokens brand-owned preserves ADR-024's neutral core: even brand #1's identity stays out of the core.
- Developing primitives against the *real* brand before deriving the style package proves them against a real theme rather than a placeholder, yet the core still never depends on the brand — the package is *derived from* stabilized values, not authored into the core. This captures the fidelity of building against the real brand without the coupling ADR-024 forbids.
- Treating the scaffold as interim — rather than freezing it (which strands the site off the design system) or discarding it up front (which blocks all portfolio progress until the core and brand repository exist) — lets portfolio work continue while the design system is built, then converge.
- Deferring the low-complexity-primitive classification avoids extracting elements whose reuse value is unproven; premature decomposition is its own cost.
- Keeping the §3.6 harness mechanism in a separate decision matches how ADR-024 surfaced it: a distinct capability with its own tradeoffs, whose inclusion here would over-scope this record.

## Consequences

- **Positive:** one coherent boundary across the design system and the portfolio; the neutral core stays brand-clean; the site gets real, shared components without a self-contained fork persisting; portfolio work is unblocked during the build-out.
- **New structural fact:** the end state implies a buzonas-brand repository distinct from the `buzonas.dev` site repository — the site consumes the buzonas brand; they are not the same repository. Standing it up is downstream work.
- **Gated on a follow-on:** the migration depends on the still-undecided §3.6 harness mechanism; until that is decided, primitives cannot be developed against the real brand in the harness, so the derive-and-migrate sequence cannot complete.
- **Narrow open item:** the classification of the low-complexity shared primitives (the mono tag chip, the section eyebrow, the status badge) remains open.
- **Interim state:** the buzonas style package (an ADR-017 artifact) does not yet exist; brand values remain in the site scaffold until derived — an expected, temporary, partial realization of ADR-017.

## Alternatives Considered

- **Freeze the site self-contained (keep the scaffold as the target).** Rejected: strands the portfolio off the design system, permanently duplicating primitives and brand tokens — the copied-source/divergence failure mode this project has repeatedly flagged — and wastes the harness the design system exists to provide.
- **Full design-system-first up front** (build the core and the buzonas-brand repository before any further site work). Rejected as the *route*, not the target: the target is precisely the site-as-consumer end state, but requiring the core and a brand repository to exist before the scaffold can proceed blocks portfolio progress for no gain the derive-and-migrate path doesn't also reach. Retained as the destination; declined as the sequence.
- **Fold the §3.6 harness mechanism into this decision.** Declined: a distinct capability with its own implementation tradeoffs; deciding it here would over-scope this record and architect ahead of its own resolution.
- **Classify the low-complexity primitives now.** Declined: their reuse value and whether they are generic vs. brand-specific are both unclear; extraction risks decomposing elements not worth reusing.
