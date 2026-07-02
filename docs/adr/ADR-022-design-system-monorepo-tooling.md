# ADR-022: Design System Monorepo Tooling — pnpm, Turborepo, Changesets

**Date:** 2026-07-02
**Status:** Accepted

## Context

ADR-017 fixed the design system's core as a **single monorepo of published workspace packages**
(a `tokens/theme-base` contract, a theme-agnostic `components` package, and a reusable
`docs-framework` shell), distributed to consumers by packaging rather than copied source or a
submodule. It deliberately **deferred the monorepo tooling** — the workspace manager, the task
orchestrator, and the versioning/publishing tool — to a later decision. This ADR settles that
deferred trio.

The choice is bound by the same forces ADR-017 and ADR-018 already established: the system must
iterate and release independently of any consumer (PRD §1.2, §4.3); components reach consumers as
published packages consumable without modification (§3.5); and the tooling should be **modern but
enterprise-common, transferable, solo-maintainable, and approachable** (the owner's stated criteria,
ADR-018). ADR-017 also treats the token/theme-base as a **versioned public surface** whose breaking
changes are semver-managed and require coordinated version bumps across packages.

This decision selects the *tooling only*. Where packages are published (registry) and under what
namespace remain open (decision-backlog; not decided here) — this ADR picks the publish *tool*, not
the publish *target*. Remote-cache backends and the exact CI workflow are implementation details.

## Decision

- **Workspace / package manager: pnpm workspaces**, pinned and provisioned via corepack.
- **Task orchestrator: Turborepo**, layered over the pnpm workspace for the task graph and build
  caching.
- **Versioning & publishing: Changesets**, using **independent per-package versioning**.

The precise configuration (`pnpm-workspace.yaml`, `turbo.json`, the Changesets config and its
GitHub Actions release workflow) is an implementation detail for the repository, not fixed here.

## Rationale

- **pnpm** is fast and disk-efficient and, decisively for a *publishing* monorepo, **strict by
  default**: it does not hoist undeclared dependencies, so a package that forgets to declare a
  dependency fails locally rather than shipping that phantom dependency to a consumer. That directly
  protects §3.5 (a consumed package must work without modification). It is a modern,
  enterprise-common choice — a transferable skill — and corepack pins the version without a global
  install.
- **Turborepo** adds a task graph and local/remote caching with minimal configuration, layered onto
  the pnpm workspace without restructuring it. It speeds repeat builds — including the
  `docs-framework`/Storybook build (ADR-019) — in local and CI runs. Its low weight suits solo
  maintenance, and adopting it now avoids a later migration once the package count grows. It was
  chosen over running no orchestrator (which is viable at three packages but leaves caching and task
  ordering on the table) and over Nx (whose power is unnecessary at this scale).
- **Changesets** is purpose-built for multi-package publishing. Its **independent per-package
  versioning** matches ADR-017's per-package-versioning rationale, and its coordinated-bump workflow
  fits the breaking-change discipline ADR-017 requires for the token-contract public surface.
  Authoring an explicit changeset per change gives deliberate semver control appropriate to a public
  API, and its first-class GitHub Actions integration aligns with the project's existing CI
  (ADR-005).

## Consequences

- **Positive:** Fast, strict installs that surface dependency-declaration errors before they reach a
  consumer; cached, ordered builds in CI; standard, well-documented tooling with strong ecosystems,
  lowering long-term maintenance risk for a solo maintainer; a versioning/publishing flow that
  matches the public-surface discipline ADR-017 set.
- **New / cost:** pnpm must be pinned via corepack in the repo and CI; contributors (currently the
  owner) must author a changeset per change; Turborepo adds a `turbo.json` and, *if* remote caching
  is later wanted, a cache backend account (optional, deferred).
- **Constraint:** This selects the publish *tool* (Changesets), but packages cannot actually be
  published until the **registry and namespace** are decided (separate open question). That decision
  gates first publish, not this one.
- **Deferred / unaffected:** The remote-cache backend and the exact CI release workflow are
  implementation details; the registry/namespace decision is separate; ADR-018's deferred
  token-generation source is unaffected.

## Alternatives Considered

- **npm workspaces** — rejected as the primary choice: zero extra tooling and maximally common, but
  its looser isolation lets phantom dependencies hide, which for a publishing monorepo can ship
  broken packages to consumers. pnpm's strictness earns its small learning cost here. npm workspaces
  remains the fallback if pnpm/corepack friction ever outweighs the benefit.
- **Yarn (Berry)** — rejected: waning momentum for greenfield projects and PnP-compatibility
  friction, with no advantage over pnpm for this repository.
- **No task orchestrator** (plain workspace scripts) — reasonable at the current three-package scale
  and the main contender against Turborepo; declined because Turborepo's caching and task graph cost
  little now and avoid a later migration. It remains the fallback if Turborepo's configuration proves
  not worth its keep.
- **Nx** — rejected: powerful (generators, affected-commands, graph visualization) but heavy in
  configuration and concepts, over-engineered at three packages, and in tension with the
  solo-maintainable/approachable criteria. Worth revisiting at greater scale (PRD Phase 3 already
  anticipates re-evaluating tooling).
- **semantic-release** — rejected: single-package-centric and commit-message-driven; multi-package
  support is plugin-heavy and gives less explicit per-package semver control than a versioned public
  surface warrants.
- **Manual versioning / publish** (`npm version` + `npm publish` per package) — rejected: no
  changelog automation and error-prone coordinated bumps, a poor fit for the token-contract
  breaking-change discipline ADR-017 requires.
