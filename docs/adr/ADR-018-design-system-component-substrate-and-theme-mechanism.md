# ADR-018: Design System Component Substrate & Theme Mechanism

**Date:** 2026-07-01
**Status:** Accepted

## Context

ADR-017 established the design system's topology — a `tokens/theme-base` contract, a theme-agnostic
`components` package, per-brand `style` packages implementing that contract, and buzonas.dev as
brand #1 — but deliberately left two things open: the component styling substrate and the mechanism
that implements the theme. This decision settles both together, because how a component consumes a
token is how it is styled; they are one decision, not two.

The choice is bound by the Design System PRD (v1.1): components must be theme-agnostic by
construction, referencing no concrete color/font/spacing value directly (§3.4); tokens and theme
values must be exposed in a machine-usable form, not only human-readable (§2.2); components must be
consumable without modification to their logic (§3.5); and released components must meet WCAG 2.1 AA
and carry their share of a consumer's performance budget (§4.2).

The substrate was reopened and evaluated on its merits rather than inherited. Although the portfolio
site already uses shadcn/ui (its ADR-003) and Tailwind (its ADR-006), the design system is a
separate system and almost no component work had been done, so there was no sunk cost forcing the
choice. It was weighed against the owner's stated criteria: a stack that is modern but
enterprise-common rather than bleeding-edge; a transferable, growth-promoting skill; solo-maintainable
for reuse in future work where the owner may be the sole engineer; and approachable to work in.

On the theme layer specifically: because a second brand is committed (PRD §2.3, ADR-017), the theme
is a contract that brands implement. A *typed, compiler-enforced* contract was considered and judged
a nice-to-have — §3.4 requires only theme-agnosticism, which semantic tokens satisfy without typing.

## Decision

- **Substrate.** Components are built as **owned source on Radix primitives, styled with Tailwind
  v4** — the shadcn/ui model. This is the same substrate as the portfolio site, chosen deliberately
  for the design system rather than inherited by default.
- **Theme mechanism.** Brand values are delivered as **semantic CSS custom properties**. Components
  reference tokens by **semantic name only**, never a concrete value (§3.4). Each brand is a distinct
  set of those semantic variables, applied via a class or data-attribute on a container — which is
  also how the documentation shell renders multiple brands at once. The set of semantic token names
  is the contract each brand implements.
- **Token sourcing (interim).** Tokens are **hand-authored** as CSS custom properties for now. A
  structured, typed token *source* that generates the CSS variables and Tailwind configuration (and
  a machine-readable export) is **deferred, not rejected** — it is the intended path to full §2.2
  satisfaction, sequenced after portfolio-MVP momentum is recovered.
- Tool choices beneath the substrate — the component-development / documentation environment, and
  the eventual token-generation tooling — are **not** decided here.

## Rationale

- shadcn/ui + Tailwind is modern and enterprise-common — a transferable, in-demand skill rather than
  a niche or bleeding-edge bet. Because it is own-the-source, there is no black-box component
  dependency to age out or churn, which suits a solo maintainer.
- Radix supplies accessible behavior, directly serving the §4.2 WCAG gate, while leaving the visual
  layer fully brand-controlled.
- Semantic CSS custom properties satisfy §3.4 structurally: components address tokens by name, a new
  brand supplies a new variable set, and no component code changes — exactly the multi-brand
  requirement. They also make the runtime multi-theme rendering the documentation shell needs
  trivial.
- A typed, compiler-enforced token contract would enforce §3.4 more strictly, but §3.4 requires only
  theme-agnosticism, not typing. Adopting one would have meant a different styling engine and
  rewriting the shadcn/Tailwind components — cost without a requirement to justify it.
- Hand-authoring tokens now trades full §2.2 machine-usability for momentum on the portfolio MVP,
  which this line of work had run ahead of. CSS custom properties remain readable by tooling, so
  §2.2 is partially met, and the structured/typed source is retained as the planned path to meet it
  fully.

## Consequences

- **Positive:** The fastest path to building components. The substrate matches the site, so the site
  consumes design-system components with no re-platforming. Radix covers the accessibility gate.
  Multi-brand theming and the documentation shell's multi-theme rendering fall out of CSS-variable
  theming naturally.
- **Positive:** Own-the-source means no upstream component dependency to track — suited to solo
  maintenance and future reuse.
- **Deferred / known gap:** Hand-authored CSS variables do not fully satisfy §2.2 — a structured,
  machine-usable token source is not yet in place. Until the deferred token-generation source is
  built, each brand's variable set is hand-maintained with nothing enforcing that brands share the
  same token shape; this discipline cost grows with each brand added.
- **Constraint:** The set of semantic token names is the de-facto contract brands implement.
  Changing it affects every brand and is managed as a breaking change, consistent with ADR-017's
  treatment of the theme-base as a public surface.
- **Open:** The component-development / documentation tool and the eventual token-generation tooling
  are not decided here.

## Alternatives Considered

- **Consume a maintained component library** (e.g., MUI, Mantine, PatternFly) — rejected:
  recognizable library defaults undercut a bespoke brand identity, and consuming a library sits in
  tension with ADR-017, which has this system *produce* its components rather than re-theme someone
  else's.
- **A headless-behavior base with a deeper accessibility pedigree** (e.g., React Aria Components)
  styled with Tailwind — a viable, slightly more accessibility-deep option, rejected because it costs
  more assembly than shadcn's pre-styled starting point for no requirement-level gain: Radix already
  clears the §4.2 accessibility gate.
- **A typed, zero-runtime styling engine with a compiler-enforced theme contract** (e.g., Vanilla
  Extract, Panda CSS) — rejected as the substrate: its headline advantage addresses a nice-to-have
  rather than a requirement, and it is newer and less enterprise-common; it would also have precluded
  using the shadcn/Tailwind components.
- **Building the structured/typed token source up front** — deferred rather than rejected (see
  Decision and Consequences): it is the right long-term answer for §2.2, sequenced after
  portfolio-MVP momentum is recovered rather than hand-authoring first.
