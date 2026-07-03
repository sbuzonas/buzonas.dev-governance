# Technical Architecture Document
## buzonas.dev Design System — Living Brand Guide & Component Playground

**Version:** 1.0
**Owner:** Steve Buzonas
**Status:** Accepted
**Last Updated:** 2026-07-03
**Companion Documents:** docs/design-system/design-system-PRD.md, docs/design-system/design-system-decision-backlog.md

---

## 1. Architecture Overview

The buzonas.dev Design System is a **brand-neutral component core**, developed and documented in isolation and distributed to consumers as **versioned npm packages**. It has no application runtime and no consuming site inside it: components are built and verified in a Storybook workshop decoupled from any consumer's build (PRD §3.2), published from a single monorepo under independent per-package semver, and themed entirely at runtime through semantic CSS custom properties (PRD §3.4). Brands — buzonas is the first — live *outside* the core in their own repositories and supply theme values to it; the core references no brand's colors, fonts, or spacing directly. The system releases on its own cadence, independent of any project that consumes it (PRD §1.2, §4.3).

The core is one monorepo of three published packages — a **theme-base** token contract, a theme-agnostic **components** package, and a reusable **docs-framework** shell (ADR-017). It emits two independent artifacts on two separate pipelines: the packages to public npm under `@sbuzonas` (ADR-023 / ADR-029), and the Storybook workshop as a static site to GitHub Pages (ADR-020). Each brand repository depends on the core one-way and produces its own **style package** (consumable by applications) and **branding site** (the docs-framework shell themed to that brand).

```
┌──────────────────────────────────────────────────────────────┐
│              Design System Core — single monorepo             │
│    tokens / theme-base  ·  components  ·  docs-framework       │
└───────────────┬───────────────────────────────┬──────────────┘
                │ Changesets publish             │ Storybook build
                ▼                                ▼
        ┌────────────────┐               ┌──────────────────┐
        │  public npm     │               │  GitHub Pages    │
        │  @sbuzonas/*    │               │  workshop / docs │
        └───────┬────────┘               └──────────────────┘
                │  install   (one-way: brands & consumers → core)
     ┌──────────┴────────────────────────────────┐
     ▼                                            ▼
┌──────────────────────────┐          ┌───────────────────────────┐
│  Brand repos             │          │  Consumer projects (any)  │
│  buzonas = brand #1      │  style   │  install core components  │
│  (+ 2nd committed)       │─ pkg ───▶│  + a brand style package  │
│  → style pkg + branding  │          │                           │
│    site                  │          │                           │
└──────────────────────────┘          └───────────────────────────┘
```

### Key Constraints Driving Architecture

- **Theme-agnostic by construction** — no component references a concrete color, font, or spacing value; all visual values arrive through semantic tokens supplied by a theme (PRD §3.4). This is the constraint the substrate, theme layer, and package boundary are all shaped around.
- **Independent cadence, standalone artifact** — the system builds, verifies, and releases fully decoupled from any consumer's build or deploy (PRD §1.2, §4.3). No consumer can gate it; it cannot gate a consumer.
- **Multi-brand from the outset** — a second, differently-branded consumer is committed, not hypothetical (PRD §2.3), so brand-independence is a present design driver. buzonas is brand #1, external to the neutral core — proving neutrality from day one.
- **One-way dependency** — brands and consumers depend on the core; the core never depends on a brand, not even as a development-time dependency of the workshop (ADR-024, PRD §3.6).
- **Consumable without modification** — components reach consumers as published packages usable without forking or rewriting component logic (PRD §3.5).
- **Released components carry the downstream bar** — components made available to consume meet WCAG 2.1 AA and do not cause a consumer to miss its own performance budget; components still being prototyped are not gated (PRD §4.1, §4.2).

---

## 2. Repository & Distribution Topology

### 2.1 Ownership & Layering

The system is organized into three ownership layers, each owning a distinct concern, with a strict **one-way dependency** running consumer → brand → core. Nothing in the core references a brand or a consumer — not even at development time (ADR-024, PRD §3.6). This is the invariant that keeps the core's cadence and neutrality intact; every other structural choice follows from it.

| Layer | Owns | Depends on | Produces |
|-------|------|-----------|----------|
| **Core** (brand-neutral) | The theme-agnostic component primitives; the semantic token contract (the set of token *names*); the docs-framework shell; a brand-neutral reference theme | — (nothing downstream) | `theme-base`, `components`, `docs-framework` packages |
| **Brand** (buzonas = #1) | A concrete set of token *values* implementing the contract; that brand's signature visual identity | the core (one-way) | a style package + a branding site |
| **Consumer** (any project) | Its own composition — routes, layouts, application components, content | the core + a brand style package | its own application |

A brand's **signature visual identity** — its distinctive brand-specific assets — is brand-owned and never enters the neutral core, *including for brand #1*. The core holds only what is brand-agnostic: the primitives, the token-name contract, and a generic reference theme (ADR-024).

### 2.2 Repositories & Packages

The core is a **single monorepo**; each **brand is a separate repository**; each **consumer is its own repository**. The core units share a toolchain and release together, so they consolidate into one repo; a brand needs independent lifecycle, ownership, and deploy cadence, so it earns a repository boundary — the reasoning is ADR-017's.

| Repository | Role | Visibility |
|-----------|------|-----------|
| Design-system core (monorepo) | The brand-neutral core: `theme-base`, `components`, `docs-framework` | Public |
| Brand repositories (one per brand; buzonas = #1) | A brand's style package + branding site | Per brand |

Concrete repository and package names are a repository-level detail, not fixed by an ADR (ADR-023 for the package scope). The three core packages:

- **`theme-base`** — the semantic token **contract**: the set of semantic token *names* plus primitives (ADR-018), published as a versioned **public surface** whose changes are breaking-change-managed (ADR-017). Brands implement it; components reference it. It also carries the brand-neutral **reference theme** the workshop renders against; whether those values live in this package or a small dedicated reference-theme package is a repository implementation detail (ADR-024).
- **`components`** — the theme-agnostic component set, built as owned source on the Radix + Tailwind substrate (ADR-018), referencing only semantic tokens. It exposes multiple style-delivery entry points and is emitted as per-module tree-shakeable ESM with client boundaries preserved — the consumption contracts are §8.
- **`docs-framework`** — the reusable Storybook shell used both for the core workshop and, themed per brand, for each brand's branding site (ADR-017 / ADR-019).

Each package versions independently on its own semver (ADR-022); the `theme-base` token contract is the versioned public surface whose breaking changes drive coordinated bumps (ADR-017). The design system's own governance documents — this TAD, the design-system PRD and decision backlog, and the shared ADRs — live in the governance companion repository, not in the core monorepo (ADR-021).

### 2.3 Distribution Mechanism

Components reach consumers as **published npm packages** — not copied source, and not a git submodule of source (ADR-017). A consumer installs from public npm under the `@sbuzonas` scope with no registry or auth configuration (ADR-023); a brand's style package is distributed the same way. The registry target, versioning, and publish flow are §9.

Because the token contract is a versioned public surface, a breaking change to it requires coordinated version bumps across the brand repositories that implement it — standard semver discipline, not a bespoke mechanism (ADR-017).

---

## 3. Monorepo Structure & Tooling

The core monorepo is managed with **pnpm workspaces**, orchestrated by **Turborepo**, and versioned and published with **Changesets** — the trio fixed by ADR-022, each on independent per-package versioning. pnpm is pinned and provisioned via corepack. Its strict, non-hoisting dependency isolation is load-bearing for a *publishing* monorepo: a package that forgets to declare a dependency fails locally rather than shipping that phantom dependency to a consumer, which directly protects consumability without modification (PRD §3.5).

| Concern | Tool | Notes |
|---------|------|-------|
| Workspace / package manager | pnpm workspaces | Corepack-pinned; strict non-hoisting isolation surfaces phantom dependencies before publish (ADR-022; serves PRD §3.5) |
| Task orchestration & caching | Turborepo | Task graph + local/remote build caching layered over the pnpm workspace (ADR-022) |
| Versioning & publishing | Changesets | Independent per-package semver; coordinated bumps for the token-contract public surface (ADR-022 / ADR-017) |

### 3.1 Workspace Layout

The layout below is **representative** — it fixes the load-bearing structure (the three core packages, the workshop, the theme-drop directory, the separate pipelines) while leaving exact directory and package names as repository-level detail (ADR-023 for the package scope):

```
design-system/                    core monorepo
├── .changeset/                   Changesets config + pending changesets (ADR-022)
├── .github/workflows/
│   ├── ci.yml                    PR checks: build · typecheck · lint · test · gates (§9, §10)
│   ├── release.yml               Changesets release-PR → npm publish (§9.2)
│   └── docs.yml                  Storybook build → GitHub Pages (§9.3)
├── packages/
│   ├── theme-base/               semantic token contract + reference theme (§5)
│   ├── components/               theme-agnostic components; multiple entry points (§8)
│   └── docs-framework/           reusable Storybook shell (§6)
├── workshop/                     core Storybook instance, renders the reference theme (§6)
├── <theme-drop>/                 gitignored brand-theme drop directory (§7; name per ADR-028)
├── pnpm-workspace.yaml
├── turbo.json
├── package.json                  root; corepack-pinned pnpm
└── tsconfig.json
```

Each package versions independently on its own semver; the `theme-base` token contract is the versioned public surface whose breaking changes drive coordinated bumps (ADR-017 / ADR-022).

### 3.2 Package Build

Each package is compiled on **two axes**, orchestrated by Turborepo (which runs the build *task* but does not itself compile) — the decision is ADR-031:

- **JS and types — `tsc`.** The TypeScript compiler emits per-module ESM — one output module per source file, tree-shakeable by construction — with `.d.ts` in the same pass, preserving each interactive module's top-of-module `'use client'` directive verbatim (§8.2). No JavaScript bundler is used, and the library is not bundled or minified: consumers re-bundle, and `sideEffects` is declared in `package.json` rather than tool-derived.
- **CSS — Tailwind v4's own build.** The prebuilt Preflight-free component-CSS bundle and the Tailwind convenience preset (§8.1) are produced by Tailwind v4's build with Preflight disabled, scoped to the component sources; theme CSS stays a separate import (§5.1).

Exact `tsconfig` options, CLI-vs-PostCSS for the CSS build, the `exports`-map wiring, and the lint rule keeping `'use client'` first in a module are repository-level details (ADR-031).

---

## 4. Component Substrate

Components are built as **owned source on Radix primitives, styled with Tailwind v4** — the shadcn/ui model — and authored in TypeScript. This substrate was selected for the design system on its own merits and deliberately, not inherited by default; the evaluation is ADR-018's.

- **Owned source, no black-box dependency.** The components are the design system's own source, not a re-themed third-party component library. There is no upstream component dependency to age out or churn — suited to solo maintenance and long-lived reuse (ADR-018).
- **Radix supplies accessible behavior.** The primitives provide keyboard interaction, focus management, and ARIA semantics — the foundation that lets *released* components clear the WCAG 2.1 AA gate (PRD §4.2). The verification regime that enforces it is §10.
- **Tailwind v4 for the visual layer.** Styling is Tailwind utilities over the primitives; the visual layer is fully brand-controlled and references only semantic tokens, never concrete color, font, or spacing values (PRD §3.4). How those utilities cross the package boundary to a consumer is §8.1.
- **TypeScript throughout**, consistent with the React / Radix / Tailwind substrate (ADR-018); the token contract and component props are typed surfaces.

The interactivity Radix's hooks and effects imply — and the server/client boundary it creates for consumers — is the RSC contract in §8.2. The theme mechanism these components reference is §5.

---

## 5. Theme Architecture

A theme is a set of **semantic CSS custom properties**. Components reference tokens by semantic *name* only — never a concrete color, font, or spacing value (PRD §3.4) — so a brand is expressed entirely as a distinct set of those variable values, with no change to component code. This is the mechanism that makes the components theme-agnostic by construction and multi-brand from the outset.

### 5.1 Theme Mechanism

- **The contract is the set of semantic token *names*,** owned by `theme-base` (§2.2). Brands implement it by supplying values; components consume it by name (ADR-018).
- **A theme is applied as a runtime CSS-variable set** on a container, selected by a class or data-attribute. Switching themes is a runtime variable swap — no recompile, no reprocessing of component CSS (ADR-018). This is also what lets the workshop render several brands at once (§6).
- **Theme CSS is always a separate import** from the component/structural CSS; the two never merge. The style-delivery mechanics that preserve this across the package boundary are §8.1.

### 5.2 Reference Theme & One-Way Dependency

The core ships a **brand-neutral reference theme** — a generic, explicitly non-brand set of values for the token contract — as the default the workshop renders against (ADR-024). It provides at least two variable sets — a generic light/dark pair being the natural form — so theme-switching can be exercised with core-only themes (PRD §3.4 verification needs at least two variable sets), and it doubles as the starting template for onboarding a new brand.

The dependency is **one-way**: the core workshop renders only against this core-owned reference theme and never depends on a brand, not even at development time (ADR-024; §2.1). Real brand values are supplied locally for iteration through the mechanism in §7, without the core acquiring any brand dependency.

### 5.3 Token Sourcing

Tokens are **hand-authored as CSS custom properties** for now. A structured, typed token *source* — one that generates the CSS variables, the Tailwind configuration, and a machine-readable export from a single definition — is **deferred, not rejected**, and is the intended path to fully satisfy the machine-usable-framework requirement (PRD §2.2); it is sequenced as later implementation (ADR-018).

In the interim, hand-authored CSS custom properties remain tooling-readable, so PRD §2.2 is **partially** met; and while hand-authored, nothing structurally enforces that every brand implements the same token *shape* — a known discipline cost the structured source will later close (ADR-018).

---

## 6. Component Workshop & Documentation

Storybook is the design system's component-development and documentation environment (ADR-019). Components are authored as stories and developed and verified in isolation there, decoupled from whether any consumer's build succeeds (PRD §3.2) — developing in isolation, rather than live on a consuming site, is the intended workflow. The reusable Storybook shell is the `docs-framework` package (§2.2), used both for the core workshop and, themed per brand, for each brand's branding site (ADR-017 / ADR-019).

### 6.1 Living Documentation

Component and brand documentation alike are authored with **Storybook Docs (MDX)** — pages that embed live components and real token values rather than describing them in prose or static images (PRD §3.1). The brand guide uses the same Storybook toolchain as the component documentation, so brand documentation can embed the live components it describes; the *tooling* for the brand guide is settled (ADR-019). Everything renders as real, inspectable examples — the live-rendering capability PRD §3.1 requires.

### 6.2 In-Workshop Verification & Interaction

- **Multi-theme toolbar.** A theme toolbar/decorator switches the active CSS-variable set, so a component can be viewed under multiple themes — the reference light/dark pair (§5.2) plus any brand themes injected locally (§7). This is the theme-switch verification PRD §3.4 calls for, exercisable against real brand values, not only the reference pair.
- **Accessibility feedback.** The a11y addon runs axe against stories as live authoring feedback while iterating — the soft gate for unreleased components (PRD §4.1). The *enforced* release-time accessibility gate is §10, not this in-loop feedback.
- **Interactive prop/theme adjustment.** Storybook controls plus theme-switching provide the interactive prop and theme adjustment of the prototyping space (PRD §3.3) — a Phase-2 capability adopted as the system matures, not required at first delivery (ADR-019).

Additional Storybook capabilities — interaction tests, visual-regression, custom addons — are deliberately **out of initial scope**; the tool is adopted for isolation and documentation first (ADR-019). Functional/interaction testing more broadly is §10 and decision-backlog row 4. The Storybook build is a **standalone static artifact** (PRD §4.3); its hosting and deploy pipeline are §9.3.

### 6.3 Brand-Guide Delivery (Deferred)

While the brand guide's *tooling* is settled (§6.1), its **delivery topology is not**: whether each brand deploys its own branding site versus a single combined theme-switching instance, and where brand-specific guide content lives relative to the brand-neutral core, are deferred. Delivery is properly a downstream brand implementation's concern and is not forced by the upstream core (ADR-019 / ADR-020). A possible future core capability that packages brand guides so several coexist in one hosting solution is a separate later decision. This question is tracked as **decision-backlog row 1**.

---

## 7. Decoupled Brand-Theme Authoring (Development Workflow)

A specific brand's theme can be authored and iterated against the core's real components and documentation toolchain **without the core acquiring any dependency on that brand — not even a development-time one** (PRD §3.6). Brand values are supplied to the workshop locally by whoever is iterating on them; the core declares no brand dependency. Because a theme is just a CSS-variable set (§5.1), the interface for supplying one is file injection, not a package link.

### 7.1 Injection Paths

A brand theme reaches the core workshop through two composable paths — a zero-config default plus an override (ADR-028):

- **Default — gitignored theme-drop directory.** A conventional, gitignored directory in the core repo. The harness scans it at startup and registers each theme file found as an additional selectable theme in the Storybook toolbar (§6.2), layered on top of the always-present reference theme (§5.2).
- **Override — environment variable.** An env var points the harness at an arbitrary path outside the drop directory — for example, a sibling brand checkout's built theme — for iterators who prefer to source the theme from elsewhere rather than copy it into the core tree.

On both paths the payload is a **CSS-variable set** (§5.1): no package link, no workspace dependency, no core→brand dependency arrow.

### 7.2 What This Guarantees

- **Nothing brand-specific is committed.** The drop directory is gitignored and the env var is machine-local; the core commits only the *interface* — the scan logic, the ignore rule, and the env-var contract — never a brand or a path to one. The PRD §3.6 "core declares no brand dependency" invariant therefore holds **structurally, not by discipline** (ADR-028).
- **The reference theme stays the default.** When neither path supplies a theme, the workshop renders against the core reference theme alone (§5.2), unchanged.
- **Multiple brands at once.** Several files in the drop directory each become a selectable toolbar entry, so multi-brand theme-switching (PRD §3.4) is exercisable in the workshop against real brand values, not only the reference light/dark pair.

This is the mechanism by which a brand author develops the core primitives against the **real** brand theme before deriving that brand's style package from the stabilized values — real-brand fidelity with no core→brand coupling.

### 7.3 Left as Implementation Detail

The directory name, the environment-variable name, whether the override points at a single file or a directory, live-reload/HMR behavior on file change, and the exact toolbar-registration wiring are repository-level details, not fixed by the decision (ADR-028). Live-reload of a dropped theme is desirable for the authoring loop but not required; the interactive prop/theme adjustment of PRD §3.3 is the separate Phase-2 capability in §6.2.

---

## 8. Consumption Contracts

A consumer integrates against two independent axes, co-shipped in the same `components` package but delivered separately: how component **styles** cross the boundary (§8.1) and how the component **JS modules** cross it (§8.2). Keeping them on separate axes is deliberate — the JS modules carry no CSS side effects, which keeps their tree-shaking clean. Both are stated here as **output contracts**; the toolchain that *produces* the artifacts — `tsc` for JS/types, Tailwind v4 for the CSS — is §3.2 (ADR-031).

### 8.1 Style / CSS Delivery

Component styling crosses the package boundary by a **scan-first model with a prebuilt path and a theme-base escape hatch** (ADR-025). Every path preserves runtime CSS-variable theming (§5.1), and a consumer wires up **exactly one** path — importing the prebuilt CSS *and* scanning would double-ship the utilities.

- **Recommended — scan-first.** Components ship as source; a Tailwind v4 consumer includes the package in its Tailwind sources, so the component utilities are generated in the consumer's single Tailwind pass — deduplicated, tree-shaken, no second Preflight. The design system provides convenience tooling (a Tailwind preset wiring the source globs and the theme-base contract) to keep this low-friction. It is the leanest combined CSS, serving minimalism and the performance gate (PRD §4.2) — recommended, not required.
- **Prebuilt — zero-config.** The package also ships a prebuilt, **Preflight-free** component-CSS bundle defaulting to the reference theme (§5.2). A consumer imports it and supplies or overrides theme variables — no Tailwind required. A common, first-class path, well suited to prototypes, quickstarts, and non-Tailwind consumers.
- **Escape hatch — build on theme-base.** The published `theme-base` (the semantic token contract, §2.2) is a stable public foundation a consumer may pave its own integration around.

The prebuilt bundle **excludes Preflight and stays variable-overridable**, so it composes with a brand's variables and never clobbers a consumer's reset (ADR-025). Theme CSS is always a separate import from this structural/utility CSS (§5.1). Which path a given consumer selects is the consumer's own choice — this section supplies the menu, not the selection. Exact entry-point names and the shape of the convenience tooling are repository-level details (ADR-025).

### 8.2 Server/Client (RSC) Contract

This axis governs the component **JS modules**. It is written for consumers on React Server Components (App Router) and is inert for non-RSC consumers, so the same package is correct for both (PRD §2.3; ADR-026).

- **The library owns the `'use client'` boundary.** Interactive components ship with the directive already applied; a consumer imports and renders them directly — including from Server Components — without wrapping them or adding directives. Requiring a wrap would be the modification consumability forbids (PRD §3.5).
- **Marking is granular, not uniform.** Only genuinely interactive components (the Radix-based ones) carry `'use client'`. Purely presentational components stay plain modules a consumer can render as Server Components, shipping no client JS — holding the client bundle to what interactivity actually requires (PRD §4.2).
- **The published build preserves per-module `'use client'` and ships tree-shakeable ESM.** Components are emitted as per-module ES modules (not a monolithic bundle), directives intact, with accurate `sideEffects` metadata — so directives survive to the consumer, and a consumer importing a subset tree-shakes the rest, and its Radix dependencies, out of the production build.

Radix providers ship as client boundaries the consumer composes directly (not wrapped); server→client prop passing follows normal RSC serialization, with interactive handlers originating in the consumer's own client components. Exact module boundaries and the `exports` map are repository-level details (ADR-026).

Emitting per-module ESM with `'use client'` preserved and correct `sideEffects` — and *excluding* a naive single-file bundle, which would strip the directives and defeat tree-shaking — is the output contract the package build satisfies; `tsc` meets it by emitting per-module output rather than a bundle (§3.2, ADR-031).

---

## 9. Distribution, Publishing & Release

Two independent pipelines release the system's two artifacts on their own triggers and targets: the **packages** to npm (§9.1–9.2) and the **Storybook docs** to GitHub Pages (§9.3). Keeping them distinct means each releases on its own terms (ADR-029 / ADR-020).

### 9.1 Registry, Namespace & Versioning

- Core packages publish to the **public npm registry** under the **`@sbuzonas`** scope — the owner's personal npm namespace (ADR-023). This covers all core packages (`theme-base`, `components`, `docs-framework`) and a brand's own style package.
- Public npm is **zero-config for consumers**: `npm install @sbuzonas/…` with no `.npmrc`, registry line, or auth token — serving consumability without modification (PRD §3.5) and open reuse (PRD §2.3).
- **Independent per-package semver** via Changesets (ADR-022): each package versions on its own track; the `theme-base` token contract is the versioned public surface whose breaking changes drive coordinated bumps (§2.2). Concrete package names within the scope are a repository-level detail (ADR-023).

### 9.2 Package-Publish Pipeline

The packages are released through a **human-gated Changesets release-PR on a trunk-based, batch-all model** (ADR-029):

```
feature PR (+ changeset) ─▶ main ─▶ Changesets bot maintains a standing
                                    "Version Packages" PR (aggregates all changesets)
                                              │
              maintainer merges  ◀─────────── sole human release act (timing = curation)
                                              │   gated: pre-publish release gate green (§10)
                                              ▼
                    version bump · changelog · git tag · changeset publish
                                              ▼
                    public npm  @sbuzonas/*   (provenance via GitHub OIDC)
```

- **Changeset per feature PR** declares release intent and the semver bump (ADR-022); the standing "Version Packages" PR aggregates all pending changesets.
- **Merging the release PR is the sole human release act** — it triggers the automated version bump, changelog, tag, and `changeset publish`. Every mechanic is automated; the human's only decision is whether and when to merge, made against the version-and-changelog preview the PR presents (ADR-029).
- **Trunk-based batch-all:** a release assembles all changesets accumulated on `main` since the previous release into one versioned unit; the curation lever is *timing* (ADR-029).
- **A reserved pre-publish release-gate stage guards the point of no return.** Because an npm publish is effectively irreversible, a release cannot publish until the gate is green (PRD §4.2). This pipeline *names and reserves* the stage; the checks and thresholds it enforces are §10.
- **The publish step produces the full entry-point set** — the style-delivery outputs (§8.1) and the per-module ESM (§8.2) — via the Turborepo build before publishing, with **provenance via GitHub OIDC** (ADR-029).
- **PR-time CI is separate from the release workflow:** build, typecheck, lint, test, and the release-gate checks run as required status checks (including on the release PR); the release workflow performs only version-and-publish (ADR-029).

A release-branch / integration-branch topology (subset curation) is a later evolution the same release-PR flow upgrades into, not part of this pipeline (ADR-029). Exact workflow YAML, action pins, and any remote-cache backend are repository-level details (ADR-022 / ADR-029).

### 9.3 Documentation Deploy Pipeline

The built Storybook is hosted on **GitHub Pages**, served as the static artifact Storybook produces (§6.2; PRD §4.3) and published by GitHub Actions — a **separate pipeline** from the package publish, with a distinct trigger and target (ADR-020 / ADR-029). A custom domain may front it; the specific hostname is a repository-level detail (ADR-020).

The platform accommodates either brand-guide delivery topology decided later (§6.3): per-brand delivery maps to each brand repository's own Pages site; a single combined instance maps to one Pages site. GitHub Pages is static-only, so Storybook-native hosting features (per-PR published Storybooks, visual regression, UI review) are not provided — deliberately, as those were deferred out of initial scope (ADR-019 / ADR-020).

---

## 10. Quality & Release Gates

Verification is gated by a component's status, not applied uniformly. While a component is being prototyped, accessibility and performance are **soft gates** — not enforced, so exploration isn't slowed (PRD §4.1). At **release**, for any component made available to consume, both become **hard gates**, verified before the irreversible npm publish (§9.2), not assumed (PRD §4.2). The regime is graduated — cheap-and-continuous at commit, representative-and-visible at PR, enforced at release (ADR-030). The story convention that marks a component "released" vs. "prototype" for gate scoping is a repository-level detail (ADR-030).

### 10.1 Accessibility

- **Automated (pre-publish gate).** The Storybook test-runner runs **axe** against each released component's stories in CI, scoped to WCAG 2.1 AA, and must report zero violations. The Storybook a11y addon runs the same axe engine as live authoring feedback in the dev loop (soft — §6.2, PRD §4.1).
- **Manual review — hybrid.** A recorded manual accessibility review (keyboard operability, focus order, screen-reader spot-check) is required for **new or changed** components at the release that first ships them; unchanged components rely on automated axe for regression. Automated axe is a *floor*, not the full bar — it is blind to keyboard operability, focus order, and semantic correctness — so the accessibility hard gate is **axe-green *and* a recorded manual sign-off on the release PR** for new/changed components (ADR-030), consistent with the human-in-the-release-loop stance (§9.2).
- **Theme/contrast boundary.** The core gate owns component-level AA (semantics, ARIA, keyboard, focus) **plus reference-theme contrast**. Color-contrast (WCAG 1.4.3) is partly a theme property; a *specific brand's* theme contrast is the brand/consumer's responsibility, verified where the brand theme is authored (the §7 harness) or in a consumer — not by the brand-neutral core (ADR-030), mirroring the neutral-core principle (§2.1).

### 10.2 Performance

Performance is measured at design time rather than deferred downstream, in a layered regime (ADR-030):

- **L0 — Structural (build-time, always).** The Preflight-free / no-duplicate-utilities properties (§8.1), the minimal client boundaries (§8.2), and tree-shakeable ESM are asserted at build. Guardrails, not measurement.
- **L1 — Weight budgets (every commit).** Bundle-size measurement on published output, **enforced** (a breach blocks), reporting per-change deltas so drift is visible even when a change stays under budget.
- **L2 — Reference-consumer E2E (deferred).** A minimal, brand-neutral, reference-themed consumer application that installs and uses the components as a real downstream project would (via §8.1's paths), measured end-to-end. Informative on PRs, a hard gate on release — **once it exists**.

**L2 is deferred until a second, differently-branded consumer exists** (PRD Phase 3): with only one consumer, a brand-neutral synthetic surface would measure a fiction; it becomes necessary — and keeps the core's own gate brand-clean (§2.1) — once no single real consumer represents the others. In the interim, the formal **pre-publish performance hard-gate is L1** in the design system's own CI (ADR-030).

### 10.3 Cadence Summary

| Layer | Commit | PR | Release |
|-------|--------|-----|---------|
| L0 structural | always | always | always |
| L1 weight budgets | enforced + delta | carried | enforced (interim perf hard-gate) |
| L2 reference-app E2E | — | informative | hard gate *(once the app exists)* |
| axe per released story | — | feedback (soft for prototypes) | enforced (zero AA violations) |
| Manual a11y review | — | — | required + recorded for new/changed |

### 10.4 Functional Testing & Implementation Detail

Functional / interaction / unit testing beyond the accessibility and performance verification above is **not decided in this document** — interaction and visual-regression testing were deferred out of initial scope (ADR-019), and the broader functional-testing approach is tracked as **decision-backlog row 4**. PR-time CI reserves a `test` status check (§9.2) for it.

Specific budget values and score thresholds (PRD §4.2 names these implementation details), the exact axe configuration and test-runner wiring, the released-vs-prototype story convention, and the manual-review record's form on the release PR are repository-level details (ADR-030).

---

*Open and deferred questions are tracked in [`design-system-decision-backlog.md`](design-system-decision-backlog.md), not enumerated here.*

*TAD v1.0 — buzonas.dev Design System*
