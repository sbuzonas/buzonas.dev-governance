# ADR-019: Storybook as the Design System Component Workshop & Documentation Tool

**Date:** 2026-07-01
**Status:** Accepted

## Context

ADR-018 set the design system's substrate — React components as owned source on Radix, styled with
Tailwind v4, themed through semantic CSS custom properties. The system still needs a tool to develop
components in isolation and to author both component and brand documentation. The substrate
constrains that choice but does not make it.

The requirements it must satisfy: components must be built, viewed, and verified in isolation,
decoupled from whether any consuming project's build succeeds (§3.2); documentation — component and
brand alike — must render as live, real examples rather than prose or static images (§3.1); the
system needs a space to adjust props and theme values interactively (§3.3, a Phase-2 target);
released components must meet WCAG 2.1 AA (§4.2); and the system must build and deploy as a standalone
static artifact (§4.3). Developing components in isolation, rather than live on a consuming site, is
also the intended workflow.

This decision names the tool and settles the tooling used to author the brand guide. It does not
decide how a brand guide is *delivered* — how and where it is deployed, and where brand-specific
content lives — which is a separate, still-open question and one that properly belongs to downstream
brand implementations.

## Decision

- **Storybook** is the design system's component-development and documentation environment.
  Components are authored as stories and developed and verified in isolation there.
- **The brand guide is authored and rendered with Storybook Docs (MDX)** — documentation pages that
  embed live components and real token values rather than describing them (§3.1). The *tooling* for
  the brand guide is settled here: it uses the same Storybook toolchain as the component
  documentation.
- **The brand guide's *delivery* is not settled here** — how and where a brand guide is deployed
  (for example, per-brand deployments versus a single combined hosting solution), and where
  brand-specific guide content lives relative to the brand-neutral core. Delivery is properly the
  right of a downstream brand implementation and is not forced by the upstream system. The design
  system may later grow foundational tooling that packages brand guides so several can coexist in one
  hosting solution; whether to build that, and how any given brand delivers its guide, are separate
  later decisions.
- Interactive prop and theme adjustment (§3.3) is provided by Storybook's controls and
  theme-switching, adopted as the system matures rather than required at first delivery.
- Implementation details left open here: the hosting target for the built Storybook, and Storybook's
  builder/framework integration. The design system is its own repository per ADR-017, independent of
  the Next.js portfolio site's stack, so its build tooling is chosen on its own terms rather than
  inherited.

## Rationale

- Storybook integrates natively with the React + Tailwind + shadcn substrate and renders components
  in isolation, directly meeting §3.2.
- Its MDX Docs support embeds live components and real values in documentation prose — the
  live-rendering capability §3.1 requires. Authoring the brand guide with the same toolchain used for
  component documentation keeps a single authoring mechanism and lets brand documentation embed the
  live components it describes, without prejudging where that guide is ultimately delivered.
- A theme toolbar or decorator switches the active CSS-variable set, so a component can be viewed
  under multiple brand themes — the verification §3.4 calls for.
- The accessibility addon runs axe against stories, supporting the §4.2 WCAG gate during development.
- Storybook builds to a standalone static site, satisfying §4.3 and decoupling the workshop from any
  consuming project's build.
- It is the established, widely-used tool for this purpose, so its ecosystem, documentation, and
  longevity lower ongoing maintenance risk for a small team.

## Consequences

- **Positive:** Components are developed and verified in isolation; the brand guide is authored with
  the same tool and can embed the live components and real values it documents; multi-brand rendering
  and accessibility checks are available in-tool; the static build deploys independently of any
  consuming project.
- **Cost:** Storybook is a larger tool with more configuration and dependency surface than a minimal
  alternative — a one-time setup cost and an ongoing dependency to keep current. This is accepted in
  exchange for the documentation capability §3.1 requires and the value of standard tooling.
- **Scope discipline:** Additional Storybook capabilities — interaction tests, visual-regression,
  custom addons — are deliberately out of scope for initial delivery. The tool is adopted for
  isolation and documentation first.
- **Open:** The brand guide's delivery (deployment topology and placement of brand-specific content,
  a downstream brand's decision), the hosting target for the built Storybook, and the choice of
  Storybook builder/framework integration are not decided here.

## Alternatives Considered

- **Ladle** — a lighter, faster isolation tool. Rejected because its documentation/MDX capability is
  thinner, and authoring a live brand reference (§3.1) is a primary requirement that rewards the
  stronger documentation tool. Ladle's advantage is lower one-time setup — a one-time cost that does
  not outweigh the documentation requirement.
- **A custom-built playground / documentation app** — rejected: it reinvents a well-solved problem
  and adds build and maintenance work disproportionate to the gain.
- **AI design-generation tooling** (e.g., Canva) — rejected: it is not a component-development
  environment, and prior direct testing showed it fabricated or dropped requested detail, making it
  unsuitable as a source of verifiable component or brand documentation.
