# ADR-023: Design System Package Registry & Namespace — Public npm under @sbuzonas

**Date:** 2026-07-02
**Status:** Accepted

## Context

ADR-017 fixed the design system as a core monorepo of published workspace packages
(`tokens/theme-base`, `components`, `docs-framework`), distributed to consumers by packaging rather
than copied source or a submodule, plus a separate style package per brand. It named — but
explicitly did not decide — the "publish/version workflow (registry access, versioning discipline)
that did not previously exist." ADR-022 then selected the publish *tool* (Changesets over
pnpm/Turborepo, with first-class GitHub Actions integration) and stated plainly that "packages
cannot actually be published until the registry and namespace are decided" — that decision, not
ADR-022, gates first publish.

This ADR settles that gate: the **registry** the core packages publish to, and the
**scope/namespace** they carry.

The forces are those ADR-017/018/022 already established: the system must be consumable without
modification (PRD §3.5) and reusable by any future project (§2.3), and its tooling should be
modern-but-enterprise-common, transferable, solo-maintainable, and approachable (owner's criteria,
ADR-018). A namespace must also be chosen: the maintainer's own identity, a brand handle
(`@buzonas`), or the FancyGuy practice identity (`@fancyguy`, cf. ADR-012's DBA under the
buzonas.dev brand).

## Decision

The design system's core packages are published to the **public npm registry** under the
**`@sbuzonas`** scope (the owner's personal npm-username namespace).

- This applies to the core monorepo packages — the tokens/theme-base, components, and
  docs-framework packages all publish as `@sbuzonas/…`.
- buzonas.dev's own brand style package (brand #1) follows the same target and scope.

The concrete package names within the scope are an implementation detail for the repository, not
fixed here.

## Rationale

- **Public npm is the lowest-friction path for consumability (§3.5) and open reuse (§2.3):** a
  consumer runs `npm install @sbuzonas/…` with no `.npmrc`, registry line, or auth token — unlike
  GitHub Packages, which requires consumer-side registry and auth configuration even for *public*
  installs. That directly serves the "consumable without modification" requirement.
- **Public visibility aligns with the portfolio-showcase intent** behind the whole system and is
  consistent with the buzonas.dev site's own public posture. The core carries no brand-specific
  values (ADR-017), so there is nothing sensitive in publishing it openly.
- **It fits the already-chosen tooling:** Changesets' publish flow and the existing GitHub Actions
  CI (ADR-005/022) target public npm as their default, best-documented path — the transferable,
  enterprise-common choice the ADR-018/022 criteria call for.
- **The maintainer is the brand, and `@sbuzonas` is his existing identity.** Publishing under the
  personal username reuses the handle the owner already uses across npm and GitHub, rather than
  standing up a separate namespace to represent him. `@buzonas` differs only trivially and denotes
  the same person, so there is no reason to establish a second handle for one identity — the
  existing username is the natural and ideal home. `@fancyguy` is the only candidate that would
  attach the packages to a *separate* brand identity (the DBA/practice) rather than the maintainer's
  own, and that separation is not warranted now.
- **`@sbuzonas` is available by default:** it is already the owner's npm username, so the scope is
  his — unlike `@buzonas`/`@fancyguy`, which would each depend on an unclaimed org being free.

## Consequences

- **Positive:** First publish is now un-gated — ADR-022's publish workflow can proceed. Consumers
  install with zero configuration.
- **Positive:** Packages sit under the maintainer's own established identity, and the core is openly
  inspectable, reinforcing the showcase purpose. If a separate published brand identity is ever
  warranted (e.g., a `@fancyguy` spin-out per ADR-012's migration trigger), a future ADR repoints
  the scope.
- **New / cost:** The packages are public; a private package on public npm would require a paid
  plan. Acceptable — the core carries no brand-specific values (ADR-017).
- **Dependency:** The owner's existing npm account (username `sbuzonas`) is the publishing identity.

## Alternatives Considered

- **GitHub Packages** — rejected as the target: co-located with source and offers free private
  packages, but requires every consumer to add an `.npmrc` registry+auth entry even for public
  installs, adding friction against §3.5/§2.3, and forces the scope to the GitHub owner. Reasonable
  if a private posture for core packages were required; it is not.
- **Private registry (Verdaccio / paid service)** — rejected: hosting or subscription overhead that
  cuts against the solo-maintainable/approachable criteria and the showcase intent, for a privacy
  the core does not need.
- **`@buzonas` scope** — rejected: trivially different from the existing `@sbuzonas` username and
  denotes the same person; no reason to establish a second handle for one identity.
- **`@fancyguy` scope** — rejected for now: attaches the packages to a separate practice/DBA
  identity rather than the maintainer's own; not warranted at this stage. Revisitable if FancyGuy
  spins out (ADR-012's migration trigger).
- **Unscoped package names** (e.g., `buzonas-design-tokens`) — rejected: no scope to group the
  package family, higher global-name-collision risk, and not the modern norm for a related package
  set.
