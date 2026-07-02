# ADR-024: Design System Reference Theme & Brand-Free Core

**Date:** 2026-07-02
**Status:** Accepted

## Context

Design System PRD §3.1 requires a living brand reference rendered as real examples, and §3.4
requires components that are theme-agnostic by construction. ADR-017 fixed the topology — a
brand-neutral core (the core "carries no buzonas-specific values"), with each brand external in its
own repository producing a `style` package. ADR-018 set the theme mechanism: brand values are
semantic CSS custom properties, and the *contract* is the set of semantic token **names**, with
values hand-authored for now. ADR-019 made Storybook the component workshop and gave it a theme
toolbar to view components under multiple themes.

That left an unresolved question: the core's own Storybook workshop must
render components *visibly*, which requires some set of token **values** — yet the core is meant to
carry no brand values. And the core↔brand dependency direction was never fixed. Until both are
settled, the TAD cannot describe how the workshop renders or how the packages depend on one another.

The apparent tension narrows once two rendering jobs are separated: the core Storybook is a
component **workshop** that needs *a* theme, not *the* brand; the live **brand reference** (§3.1) is
produced downstream in each brand repo's branding site — ADR-017's shared shell themed per brand.
Neutrality and live-themed rendering therefore happen in different places.

## Decision

- **The core ships a brand-neutral reference theme** — a generic, explicitly non-brand set of values
  for the semantic token contract (ADR-018) — as the default theme the core workshop renders
  against. Generic variants (e.g. a light/dark pair) may be included to exercise theme-switching
  (§3.4) without introducing any brand. Where the reference values live (contract-package fallbacks
  vs. a small dedicated reference-theme package) is an implementation detail, not fixed here.
- **The dependency direction is one-way: brands depend on the core; the core never depends on a
  brand — not even as a development-time dependency of the workshop.** The core workshop renders only
  against the core-owned reference theme.
- Iterating a *specific brand's* theme against the core components and toolchain — without coupling
  the core to that brand — is a capability this decision surfaced as worth having. Its **mechanism is
  not decided here**; the capability is being captured as a new design-system PRD feature line and a
  new decision-backlog open question, to be resolved by a later ADR.

## Rationale

- Separating workshop-rendering from brand-guide-rendering dissolves the tension: the workshop needs
  *a* theme, and §3.1's live brand reference is a brand-repo artifact, not a core one.
- A core-owned reference theme is continuous with the shadcn substrate (ADR-018), which already ships
  default CSS-variable values a consumer overrides; it doubles as a starting template for onboarding
  the committed second brand (PRD §2.3, Phase 3).
- One-way dependency preserves ADR-017's core rationale — even brand #1 is external, so the core
  stays theme-clean and its cadence is not coupled to any brand's release. A dev-only dependency on a
  brand would reintroduce exactly that coupling and privilege brand #1 inside the neutral core.
- §3.4 theme-switch verification needs ≥2 variable sets, satisfiable with core-owned generic themes;
  it does not require a brand theme, so a brand-free core costs nothing there.

## Consequences

- **Positive:** The workshop renders and components are developed with zero brand coupling; the
  reference theme seeds new brands; ADR-017's neutrality and cadence-decoupling hold end to end.
- **Positive:** The TAD can now describe workshop rendering and a one-way package dependency graph.
- **New / known gap:** The reference theme is another value set the core maintains — small, and it is
  the shadcn-style default regardless — hand-authored per ADR-018 until the deferred token-generation
  source lands.
- **Deferred:** The mechanism for locally supplying a real brand's theme to the harness for
  brand-theme iteration is not decided here — see the new PRD feature line and the new backlog open
  question.

## Alternatives Considered

- **Harness-only demo values (ship no reference theme)** — rejected: renders the workshop but ships
  nothing reusable, gives the second brand no starting template, and doesn't express the shadcn
  default-theme pattern.
- **Theme-to-render (core dev-depends on a brand `style` package)** — rejected: highest workshop
  fidelity, but points the dependency arrow core→brand, couples core development to a downstream
  brand's cadence, and puts brand #1 back inside the neutral core, contrary to ADR-017. The desired
  fidelity is recoverable without the coupling via the separately-decided local plug-in capability.
- **Deciding the plug-in mechanism in this ADR** — declined: it is a distinct capability with its own
  requirement and implementation tradeoffs; folding it in would over-scope this decision and architect
  ahead of the PRD, contrary to the PRD→ADR ordering. Captured as a requirement and an open question
  instead.
