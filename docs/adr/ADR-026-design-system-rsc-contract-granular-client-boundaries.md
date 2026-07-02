# ADR-026: Design System Server/Client (RSC) Contract — Library-Owned Granular Client Boundaries

**Date:** 2026-07-02
**Status:** Accepted

## Context

An open question remained about the **server/client contract** for consumers of the design
system's Radix-based components. Radix primitives rely on hooks, context, and effects (ADR-018), so
any interactive component built on them is inherently a React Client Component and must carry the
`'use client'` directive. The committed first consumer, buzonas.dev, is a Next.js App Router /
React Server Components application (ADR-001), where modules are Server Components by default and
`'use client'` marks the boundary into client rendering. What was unsettled: who owns that boundary,
and how a server-first consumer uses the components without friction — which bears directly on
consumability (§3.5, usable without forking or rewriting) and on not inflating a consumer's client
bundle beyond what it needs (§4.2, and the minimalism the owner prioritizes).

Two facts frame the decision. A Server Component can render a Client Component with no issue — so
"Radix is client-side" and "the consumer is server-first" do not conflict; the component simply
becomes a client boundary in the tree. And the `'use client'` directive is inert in non-RSC
bundlers — so shipping it does not harm a non-RSC consumer (§2.3, reuse by any future project).

## Decision

- **The library owns the `'use client'` boundary.** Interactive components ship with the directive
  already applied; a consumer imports and renders them directly — including from Server Components —
  without wrapping them or adding directives. Requiring a consumer to wrap a component to make it
  work would be the modification §3.5 forbids.
- **The marking is granular, not uniform.** Only components that are actually interactive (the
  Radix-based ones) carry `'use client'`. Components with no interactivity remain plain modules a
  consumer can render as Server Components, shipping no client JS. This holds the client bundle to
  what interactivity genuinely requires, and mirrors the discipline the consuming site already
  applies to its own components.
- **The published build preserves per-module `'use client'` directives and ships tree-shakeable
  ESM.** Components are emitted as per-module ES modules (not a monolithic bundle), with directives
  intact and accurate `sideEffects` metadata, so the directives survive to the consumer and a
  consumer importing a subset tree-shakes the rest — and its Radix dependencies — out of the
  production build.

Radix providers ship as client boundaries the consumer composes directly (not wrapped), and
server→client prop passing follows normal RSC serialization rules (interactive handlers originate in
the consumer's own client components). The exact module boundaries and `exports` map are
implementation details, not fixed here.

## Rationale

- **§3.5:** the library owning the boundary means an RSC consumer uses components as-is — no
  wrapping, no directives to add.
- **§4.2 / minimalism:** granular marking keeps purely presentational components server-rendered
  (zero client JS), and per-module ESM plus `sideEffects` lets the consumer eliminate unused
  components and their Radix dependencies — so packaging carries no dead-code cost when built this
  way.
- **§2.3:** `'use client'` is a no-op in non-RSC bundlers, so the same package is correct for RSC and
  non-RSC consumers alike.
- **Consistency:** granular marking matches the consuming site's existing practice of avoiding
  `'use client'` unless required, reinforcing rather than diverging from how components are already
  authored.
- **One rule, three payoffs:** per-module ESM output (no monolithic bundle) is simultaneously what
  preserves the directives, what enables consumer tree-shaking, and what keeps the client boundary
  granular.

## Consequences

- **Positive:** RSC consumers use components directly with zero wrapping; presentational components
  stay server-rendered; unused components and their dependencies tree-shake out; non-RSC consumers
  are unaffected.
- **Build constraint:** the publish build must emit per-module ESM with `'use client'` preserved and
  correct `sideEffects` — a concrete requirement for the still-to-be-defined package-publish
  pipeline. A naive single-file bundle, which would strip the directives and defeat tree-shaking, is
  excluded.
- **Design guideline:** components likely to be rendered directly by a consumer's Server Components
  keep serializable-friendly prop APIs; components needing function/handler props are composed within
  the consumer's client tree, per normal RSC rules.
- **Independent of CSS delivery:** this contract governs the JS modules only; how component CSS
  crosses the boundary is a separate axis co-shipped in the same package. Because CSS is delivered on
  its own axis, the JS modules carry no CSS side effects, which keeps their tree-shaking clean.

## Alternatives Considered

- **Uniform `'use client'` (mark every component module)** — rejected: simplest to author, but it
  makes purely presentational components Client Components, shipping client JS an RSC consumer does
  not need. It does not break tree-shaking (unused code is still eliminated), but it forfeits server
  rendering for *used* presentational components, against the §4.2/minimalism aim.
- **Consumer applies the directive (ship unmarked, consumer wraps)** — rejected: pushes per-component
  wrapping onto the consumer — the modification §3.5 forbids.
- **Rely on Next `transpilePackages` as the contract** — rejected as the primary mechanism: a
  consumer-side, Next-specific build lever, not a portable contract, and no substitute for shipping
  correct directives. It remains available to a consumer as a compatibility measure, not the design.
- **Monolithic bundle output** — rejected: strips/hoists `'use client'` and defeats consumer
  tree-shaking; incompatible with both the granular contract and the minimalism aim.
