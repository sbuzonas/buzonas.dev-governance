# ADR-028: Design System Decoupled Brand-Theme Authoring — Local Theme Drop-Directory with Env Override

**Date:** 2026-07-02
**Status:** Accepted

## Context

Design System PRD §3.6 (Decoupled Brand-Theme Authoring) requires that a specific brand's theme can
be authored and iterated against the core's real components and documentation toolchain **without the
core acquiring any dependency on that brand — not even a development-time one.** The brand values are
supplied locally by whoever is iterating on them; the core declares no brand dependency.

Several accepted decisions bound the shape of any mechanism that satisfies this:

- **ADR-018** fixed a theme as a set of semantic CSS custom properties, imported separately from the
  component/structural CSS and applied as a runtime variable swap. The entire payload a theme carries
  is therefore a CSS-variable set — not a build artifact and not necessarily a package.
- **ADR-019** made Storybook the component workshop and gave it a theme toolbar for viewing
  components under multiple themes.
- **ADR-024** established the one-way dependency direction — brands depend on the core; the core never
  depends on a brand, not even as a development-time dependency of the workshop — had the core ship a
  brand-neutral reference theme it renders against by default, and explicitly surfaced this
  local-brand-iteration capability while leaving its mechanism undecided.
- **ADR-027** made the portfolio site a consumer whose primitives are developed in the core harness
  against the **real** buzonas brand styles supplied locally, then derived into a style package; it
  deferred this mechanism and gated the derive-and-migrate sequence on it.

The open question this record settles: **how does a real brand's CSS-variable set get in front of the
core workshop for local iteration, without the core committing anything brand-specific or acquiring a
dependency — dev-time included — on the brand?**

## Decision

A brand's theme is injected into the core workshop **locally**, as a CSS-variable set (ADR-018),
through two composable paths — a zero-config default plus an override:

- **Default — a conventional, gitignored theme-drop directory** in the core repository. The harness
  scans it at startup and registers each theme file found as an additional selectable theme in the
  Storybook toolbar (ADR-019), layered on top of the always-present reference theme (ADR-024).
- **Override — an environment variable** that points the harness at an arbitrary path outside the
  drop directory (for example, a sibling brand checkout's built theme), for iterators who prefer to
  source the theme from elsewhere rather than copy it into the core tree.

Consequences of the shape:

- **Nothing brand-specific is committed.** The drop directory is gitignored and holds only local
  content; the environment variable lives on the iterator's machine. The core commits only the
  *interface* — the scan logic, the ignore rule, and the env-var contract — never a brand or a path
  to one. The §3.6 "core declares no brand dependency" invariant holds structurally, not by
  discipline.
- **The reference theme remains the default.** When neither path supplies a theme, the workshop
  renders against the core reference theme alone (ADR-024), unchanged.
- **The payload is a CSS-variable set on both paths** (ADR-018) — no package link, no workspace
  dependency, no core→brand arrow.
- **Multiple themes may be present at once** — several files in the drop directory, each a selectable
  toolbar entry — which exercises §3.4 multi-brand theme-switching in the workshop against real brand
  values rather than only the reference light/dark pair.

The directory name, the environment-variable name, whether the override points at a single file or a
directory, the live-reload/HMR behavior on file change, and the exact toolbar-registration wiring are
implementation details for the repository, not fixed here.

## Rationale

- **A theme is just CSS variables (ADR-018), so a file-injection interface is sufficient** — a package
  link buys no theming fidelity over a variable file. This delivers the real-brand fidelity ADR-027
  wants (the harness renders the *actual* brand variables) while the core still declares no brand
  dependency, which is exactly the coupling ADR-024 forbade.
- **Gitignored drop directory + machine-local env var keeps the core brand-clean by construction.**
  Nothing brand-specific ever enters the committed surface, unlike a package-link approach where a
  committed workspace manifest or lockfile can leak the link.
- **The default-plus-override shape mirrors ADR-025** (recommended path plus escape hatch): the scan
  directory is zero-config for the common case — drop a file and it appears — while the env override
  handles sourcing a theme from a sibling checkout without copying it in. Keeping the same mental
  model across the repo lowers cognitive load.
- **Scanning a directory naturally supports several themes simultaneously**, so §3.4 multi-brand
  switching is verifiable in the workshop with real values, not only the reference pair.
- **It unblocks ADR-027's derive-and-migrate:** primitives can now be developed against the real
  buzonas theme in the harness, after which the buzonas style package is derived from the stabilized
  values.

## Consequences

- **Positive:** the core stays brand-clean by construction; real-brand iteration in the workshop is
  unblocked, clearing ADR-027's migration gate; multi-brand switching is exercised with real values;
  the escape-hatch shape is consistent with ADR-025.
- **Positive:** the mechanism is brand-generic — every brand's iterator uses the same drop-dir/env
  contract; nothing is buzonas-specific, consistent with the multi-brand posture (§2.3, §3.6).
- **New / cost:** the harness gains theme-discovery logic (scan the directory, read the env var) and
  toolbar-registration wiring; a `.gitignore` entry must reliably exclude the drop directory so brand
  content cannot be committed by accident.
- **Constraint:** injected themes are the separate theme-CSS import (ADR-018/025), never merged into
  the component CSS; the reference theme stays the default whenever nothing is supplied.
- **Deferred / implementation detail:** the directory name, the env-var name, single-file-vs-directory
  for the override, and live-reload behavior are left to the repository. Live-reload of a dropped theme
  is desirable for the authoring loop but not required by this decision; §3.3 interactive prop/theme
  adjustment is a separate Phase 2 capability.
- **Unchanged / out of scope:** how a brand *produces* its theme file (hand-authored per ADR-018 until
  the deferred token-generation source lands) is not affected by this decision.

## Alternatives Considered

- **Env-pointed file only (no drop directory).** Clean and zero-repo-footprint, but requires setting a
  variable even for the common case and does not naturally surface multiple brands at once. Retained as
  the *override* path rather than the whole mechanism.
- **Drop directory only (no env override).** Zero-config, but forces copying an externally built theme
  into the core tree to iterate on it. The env override removes that copy step for a sibling-checkout
  workflow. Retained as the *default*, not the whole.
- **Linked local brand package (pnpm link / local `file:` reference).** Highest package-shape fidelity,
  but it points the dependency arrow core→brand — the coupling ADR-024 explicitly rejected — and risks
  leaking into committed workspace manifests/lockfiles. For a *theme* it buys no fidelity over a
  CSS-variable file (ADR-018), so the coupling is unjustified. Rejected.
- **Core dev-dependency on the brand style package (author the brand into the workshop).** Already
  rejected by ADR-024: it recouples the core's cadence to a brand and privileges brand #1 inside the
  neutral core. Rejected for the same reasons here.
