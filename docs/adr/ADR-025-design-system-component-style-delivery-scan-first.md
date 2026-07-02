# ADR-025: Design System Component Style Delivery — Scan-First with Prebuilt Fallback

**Date:** 2026-07-02
**Status:** Accepted

## Context

An open question remained about **how component styles cross the package boundary**: concretely,
whether the package ships precompiled CSS or ships the Tailwind source classes that a consumer's
build scans. ADR-017 distributes components as published packages (not copied source, not a
submodule); ADR-018 builds them as owned source on Tailwind v4 with semantic-CSS-variable theming;
ADR-024 has the core ship a brand-neutral reference theme. The Tailwind utility class names in the
component source (`flex`, `px-4`, `bg-primary`) are inert strings until *some* build emits their
CSS — this decides which build does that, and when.

Two things are constant and are *not* what this decides. Theming is a **runtime CSS-variable swap**
(ADR-018), so re-theming never requires reprocessing the component CSS; and the theme CSS (the
active variable set) is always a separate import from the component/structural CSS. The boundary
question is therefore about the **utility/structural CSS only**, and it is driven by leanness and
performance rather than by theming.

The binding constraints are §3.4 (theme-agnostic via CSS variables), §3.5 (components ship complete
— usable without forking or rewriting a component), and §4.2 (released components must not be the
cause of a consumer failing its performance budget), together with the owner's stated tenets of
minimalism and performance.

## Decision

Component styles cross the boundary by a **scan-first model with a prebuilt path and a theme-base
escape hatch**:

- **Recommended path — scan-first.** Components ship as source; a Tailwind v4 consumer includes the
  package in its Tailwind sources so the component utilities are generated in the consumer's single
  Tailwind pass — deduplicated, tree-shaken, with no second Preflight. The design system provides
  convenience tooling to make this path low-friction (wiring the source globs and the theme-base
  contract). It is the recommended path, **not** a required one.
- **Prebuilt path — zero-config with plugged variables.** The package also ships a prebuilt,
  Preflight-free component CSS bundle defaulting to the reference theme (ADR-024). A consumer imports
  the prebuilt CSS and supplies or overrides theme variables (the reference theme by default, a
  brand's set otherwise) — no Tailwind required. This is not the optimized path, but it is a
  **common and fully acceptable** one: its zero-config immediacy makes it well suited to prototypes
  and quickstarts, as well as to non-Tailwind consumers.
- **Escape hatch — build on the theme-base.** The published theme-base (the semantic token contract,
  ADR-018) is a stable public foundation; a consumer may pave its own integration around it.

Every path preserves runtime CSS-variable theming. A consumer wires up **one** path — importing the
prebuilt CSS *and* scanning would double-ship the utilities. The exact entry-point names, the shape
of the convenience tooling, and the build steps are implementation details, not fixed here.

## Rationale

- **Minimalism and performance (owner tenets; serves §4.2):** scan-first yields the leanest combined
  CSS — each used utility emitted once across app and library, no duplicated Preflight — so the lean
  path is the recommended one.
- **§3.5 is satisfied on every path:** components ship complete, and none of the paths requires
  forking or rewriting a component (scanning reads class names, the prebuilt bundle is import-only,
  the theme-base is a contract).
- **§3.4 is satisfied on every path:** theming remains a runtime variable swap regardless of how the
  utility CSS is delivered.
- **The prebuilt path is a first-class, common choice (the soft §2.3 reading):** its zero-config
  immediacy serves prototypes, quickstarts, and non-Tailwind consumers directly — a legitimate
  everyday path, not merely insurance — while the recommended scan path stays lean for consumers who
  want it.
- **Convenience without coercion:** providing tooling for the scan path removes its one real friction
  (manual source configuration), so "recommended" is also "easy"; keeping it non-required preserves
  consumer freedom to take the prebuilt path or build their own on the theme-base.
- **The scan-path consistency risk is contained:** raw utilities resolving against the consumer's
  scale is neutralized by routing consistency-critical dimensions through semantic tokens (ADR-018)
  and by the convenience tooling carrying the design system's own scale.

## Consequences

- **Positive:** Lean-by-default for the primary (Tailwind) consumers; a zero-config prebuilt path
  preserves reach; the theme-base as a stable contract enables bespoke integrations.
- **New / cost:** The package exposes multiple entry points — scannable source, a Tailwind
  convenience configuration, and a prebuilt Preflight-free CSS bundle carrying the reference theme.
  That is more build and publish surface, which the eventual package-publish pipeline will need to
  produce and release.
- **Docs / test surface:** Two supported integration paths to document and test; the recommended one
  is scan-first.
- **Constraint:** The prebuilt bundle must exclude Preflight and stay variable-overridable, so it
  composes with a brand's variables and never clobbers a consumer's reset.
- **Leaves the consuming site's own choice open:** which path the buzonas.dev site itself takes —
  scan vs. prebuilt, and whether it drops its own `globals.css`/Tailwind config — is a separate,
  still-open question; this ADR supplies the menu, not the site's selection.

## Alternatives Considered

- **Prebuilt-only (as the primary path)** — rejected as primary: robust and zero-config, but not
  maximally lean (it duplicates utilities shared with the consumer's app and ships a fixed bundle).
  Retained as a common, first-class path — just not the primary/optimized one.
- **Scan-only (no prebuilt)** — rejected: the leanest, but it assumes every consumer is a Tailwind v4
  project; keeping the prebuilt path first-class serves prototypes, quickstarts, and non-Tailwind
  consumers as an everyday option.
- **Prebuilt-default with opt-in scan (the other hybrid orientation)** — rejected in favor of
  scan-first: the owner prioritizes minimalism and performance and wants the lean path to be the
  recommended one, with prebuilt as the common secondary path rather than the blessed default.
- **Copied source / shadcn CLI distribution** — out of scope: already excluded by ADR-017 in favor
  of published packages.
