# ADR-031: Design System Package Build Tooling — tsc for Per-Module ESM, Tailwind for Prebuilt CSS

**Date:** 2026-07-03
**Status:** Accepted

## Context

The design system's core packages must be compiled into their publishable artifacts, and prior decisions fixed the **output those artifacts must have** without naming the tool that produces them:

- **ADR-026** fixed the JS output contract: components are emitted as **per-module, tree-shakeable ES modules** (not a monolithic bundle), with each interactive module's `'use client'` directive **preserved verbatim** and accurate `sideEffects` metadata, so a consumer tree-shakes unused components and their Radix dependencies out.
- **ADR-025** fixed the style delivery: alongside scannable source and a Tailwind convenience preset, the package ships a **prebuilt, Preflight-free component-CSS bundle**.
- **ADR-018** fixed the substrate: owned source on Radix + Tailwind v4, authored in TypeScript.
- **ADR-022** selected Turborepo as the task orchestrator but stated plainly that it orchestrates the build *task* and does not itself compile — leaving the compiler unchosen.

What remained open is **which tool compiles each package's publishable JS and CSS artifacts**. This is correctness-sensitive rather than cosmetic: several common JavaScript bundlers strip or hoist the `'use client'` directive or defeat tree-shaking, which would silently break the ADR-026 contract. The requirement it serves is consumability without modification (PRD §3.5) — a consumer must be able to install and use the packages as-is — and the performance/minimalism bar on released components (PRD §4.2), together with the owner's standing minimalism and solo-maintainability tenets (ADR-018).

## Decision

The core packages are built with **two compilers on two axes**, orchestrated by Turborepo (ADR-022):

- **JS and types — `tsc`.** The TypeScript compiler emits each package as **per-module ESM**, one output module per source file, with `.d.ts` declarations produced in the same pass. Because the emitted module graph mirrors the source, the output is per-module and tree-shakeable by construction, and the top-of-module `'use client'` directive — a string-literal statement — is preserved in place. No JavaScript bundler is used. The library is **not** bundled or minified; consumers re-bundle. `sideEffects` is declared in each package's `package.json` (JS modules side-effect-free; CSS files listed), not derived by a tool.
- **CSS — Tailwind v4's own build.** The prebuilt **Preflight-free component-CSS bundle** and the **Tailwind convenience preset** (ADR-025) are produced by Tailwind v4's own build pipeline with Preflight disabled, scoped to the component sources. The active theme CSS remains a separate import from this structural/utility CSS (ADR-018 / ADR-025).

Left as repository-level implementation detail: the exact `tsconfig` compiler options (module/target/moduleResolution), whether the CSS build runs via the Tailwind CLI or a PostCSS pipeline, the `exports`-map wiring, and the lint rule or convention that keeps `'use client'` as a module's first statement.

## Rationale

- **tsc satisfies the ADR-026 contract by construction, not by plugin.** It emits one module per source file, structurally preserving the module graph — which *is* the per-module, tree-shakeable ESM the contract requires — and it has nothing that would hoist or strip the `'use client'` statement. The correctness-critical criterion is met without a bundler-specific directive plugin that could regress on upgrade.
- **It is the leanest possible toolchain.** No bundler dependency at all, no bundler configuration to age out — the strongest fit for the minimalism and solo-maintainability tenets (ADR-018), with a smaller surface than any bundled alternative.
- **Bundling buys nothing here.** Because the contract is explicitly per-module and consumers re-bundle and tree-shake (ADR-026), pre-bundling or minifying in the library would work *against* the contract, not for it. tsc's lack of bundling is therefore alignment, not a gap.
- **Types come free in the same pass**, covering the typed-surface need (ADR-018) without a separate dts bundler.
- **The CSS artifacts are a Tailwind concern, and Tailwind v4 is already the engine (ADR-018),** so producing the Preflight-free bundle and preset from Tailwind's own build keeps the CSS toolchain singular and adds no bundler. Keeping JS (tsc) and CSS (Tailwind) as separate steps mirrors the separate-axis delivery ADR-025 / ADR-026 already established.

## Consequences

- **Positive:** minimal dependency and configuration surface; the RSC per-module/`'use client'` contract holds by construction; `.d.ts` emitted in-pass; each axis keeps a single toolchain; nothing to migrate if a bundler churns. Turborepo caches both build tasks with no remote-cache requirement added.
- **Constraint — directive position.** For tsc to keep `'use client'` at the top of an emitted module, it must remain the module's first statement; a lint rule or authoring convention enforces this (implementation detail).
- **Constraint — hand-declared `sideEffects`.** Because tsc does not derive it, the `sideEffects` field is maintained by hand and must stay accurate (CSS listed, JS side-effect-free) — a small, ongoing discipline cost.
- **Constraint — no library-side minification/bundling.** Intended: consumers re-bundle. If a pre-bundled or minified artifact is ever required (for example, a CDN/`<script>` build), that is a new decision, not covered here.
- **Handoff:** this fixes how the ADR-029 pipeline's build step produces artifacts, unblocking that pipeline's build stage. First *gated* publish still depends on the ADR-030 release-gate wiring; this decision does not touch it.

## Alternatives Considered

- **bunchee** — an RSC-aware, near-zero-config bundler with first-class `'use client'` and automatic dts. Rejected as unnecessary: for a per-module library, tsc already produces contract-correct output with no bundler at all, so bunchee would add a dependency for bundling/entry-inference the per-module contract does not need. A reasonable fallback if a tsc-only setup ever proves insufficient.
- **Rollup + a preserve-directives plugin** — the battle-tested RSC-library path with full control. Rejected: the heaviest configuration and dependency surface, and its bundling/code-splitting/minification power is precisely what the per-module contract makes moot; tsc reaches the same output more simply. Revisit only if a bundled artifact becomes required.
- **tsup (esbuild)** — fast and low-config, but esbuild has historically hoisted or stripped `'use client'`, putting the correctness-critical criterion at risk and requiring workarounds. Rejected: it trades ADR-026 safety for a build-speed benefit that does not matter at this scale.
- **Vite library mode / unbuild** — per-module output with preserved directives is either awkward or effectively Rollup wrapped, with no advantage over the simpler tsc path. Rejected.
- **A single tool spanning JS and CSS** — rejected: JS and CSS deliberately cross the package boundary on separate axes (ADR-025 / ADR-026); forcing one tool over both would couple axes that are intentionally independent.
