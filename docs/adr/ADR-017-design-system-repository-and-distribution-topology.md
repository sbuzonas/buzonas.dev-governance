# ADR-017: Design System Repository & Distribution Topology

**Date:** 2026-07-01
**Status:** Accepted

## Context

The buzonas.dev Design System PRD (v1.1, Accepted) requires an iteration and release cadence
independent of any consuming project (§1.2, §4.3), theme-agnostic components that reference no
brand values directly (§3.4), consumability without modification to component logic (§3.5), reuse
by any future project (§2.3), and a living brand reference rendered as real examples rather than
prose (§3.1). PRD v1.1 further establishes that at least one additional, differently-branded
consumer beyond buzonas.dev is committed — multi-brand is a present design driver, not a
hypothetical to evaluate later (§2.3, §5 Phase 3).

This decision settles two related structural questions together: **where the design system
physically lives**, and **how a consuming project obtains its components**. Earlier deliberation had
already narrowed the first — the system lives in its own repository rather than a folder in the site
repo (which would couple its cadence to the site, violating §1.2/§4.3) or in the governance repo
(which is documentation-only by charter) — and had established that components are distributed by
packaging rather than by copied source or a submodule of source. What remained open was the
*internal topology*: one repository or several, and along which seams, given the committed
multi-brand requirement.

ADR-011's repository philosophy governs where those seams fall: scope a repository when toolchain,
release cadence, access control, or ownership are meaningfully distinct; consolidate when artifacts
share a pipeline and deploy together and separation would only impose cross-repo coordination for
routine changes.

Because components are theme-agnostic (§3.4) and a second brand is committed, there is no single
fixed token set for one location to "own" — the theme layer is a contract that brands implement.
The mechanism that implements that contract is a separate, still-open decision and is not settled
here.

## Decision

The design system's repository structure and its component-distribution mechanism are decided
together, as a single coherent topology.

- The design-system **core is a single monorepo**, published as versioned workspace **packages**:
  a **tokens / theme-base** package (the semantic token contract plus primitives), a **components**
  package (theme-agnostic — references only semantic tokens from the contract), and a
  **docs-framework** package (the reusable branding-guide and component-showcase shell).
- Components reach consumers as **published packages** — not copied source, and not a git submodule
  of source.
- **Each brand/theme lives in its own separate repository**, depending on the core packages and
  producing two artifacts: a **style package** (the theme implementing the token contract,
  consumable by applications) and a **deployed branding site** (the docs-framework shell themed to
  that brand).
- **buzonas.dev is brand #1.** The core carries no buzonas-specific visual values.

This decision is deliberately **tool-agnostic**. It does not select the component-development and
documentation tool, the mechanism that implements the theme contract, or the deployment/hosting
target — those remain open decisions.

## Rationale

- **Package boundaries inside the core** give independent per-package versioning and a clean
  consumer dependency surface — a project consuming components does not pull the docs-framework's
  development dependencies — while incurring none of the cross-repo coordination cost of separate
  repositories. This satisfies §1.2/§4.3/§3.5 at the lowest overhead. Per ADR-011's test, the core
  units share a toolchain and release together, so they consolidate.
- **The brand line is where a repository boundary earns its cost.** A brand — especially a client
  brand or a separate venture — needs independent lifecycle, ownership/visibility, and deploy
  cadence that a package inside the core repo cannot cleanly provide. Multi-brand is committed
  (PRD §2.3), so this is a present need, not speculation. Per ADR-011's test, brands are scoped.
- **Packaging as the distribution mechanism** matches the monorepo-of-packages pattern, gives clean
  semver boundaries, and avoids both the copied-source duplication/divergence failure mode flagged
  repeatedly in this project's history and the local-file-access friction of a submodule-of-source.
- **A published theme-base contract** lets each brand implement it independently, satisfying §3.4
  structurally: components depend on the contract; brands supply the values.
- **A reusable docs-framework package** means each brand's branding site is the same shell themed
  differently, satisfying §3.1's living brand reference per brand without rebuilding the shell.
- **buzonas.dev as an external brand #1** keeps the core honestly theme-agnostic from day one — the
  first theme is proven from outside the core, not baked in.

## Consequences

- **Positive:** Iteration and release cadence are independent of any consuming project; the consumer
  dependency surface is clean; the system is structurally multi-brand from the outset; each brand
  gets its own living branding site from a shared shell.
- **Positive:** The core stays theme-clean because even brand #1 is external to it.
- **New:** Multiple repositories to maintain — one core monorepo plus one repository per brand.
  Cross-repo dependency is via published packages, which requires a publish/version workflow
  (registry access, versioning discipline) that did not previously exist in this project.
- **Constraint:** The tokens/theme-base package is a public API surface; changes to the token
  contract are breaking-change-managed, and a breaking core change requires coordinated version
  bumps across brand repositories (standard semver discipline).
- **Deferred:** The monorepo tooling (workspace manager, task orchestrator, versioning/publishing
  tool), the component-development and documentation tool, the theme-contract mechanism, and the
  deployment/hosting target are not decided here.
- **Unaffected:** Where the design system's own governance and decision history live is not decided
  here; it continues to follow ADR-011's governance-companion precedent.

## Alternatives Considered

- **Single repository, single package** (everything together; the playground as a dev harness over
  the same source) — rejected: no clean seam between the consumable components and the
  docs/playground development dependencies, and no structural expression of the committed brand
  separation.
- **Single monorepo with brands as internal packages** — rejected as the brand realization: it
  couples every brand's release and visibility to the core repository; a client or separate-venture
  brand should not necessarily live in the core repo. This fails ADR-011's independent-ownership /
  visibility test for the brand unit. (The monorepo *is* retained for the core units, where the
  same test points the other way.)
- **Full polyrepo** (separate repositories for tokens, components, docs-framework, and each brand)
  — rejected: the core units share a toolchain, release together, and have no independent-ownership
  need; separate repos would impose cross-repo coordination for routine core changes (ADR-011's
  "consolidate when" test) for no benefit.
- **Copied source or git submodule for component distribution** (instead of packaging) — rejected:
  copied source recreates the duplication/divergence failure mode flagged repeatedly in this
  project's history; submodule-of-source imposes local-file-access friction and gives no clean
  semver boundary between the core and its consumers.
- **Folder in the site repo / folder in the governance repo** — rejected in earlier deliberation:
  the former couples the system's cadence to the site (violating §1.2/§4.3); the latter conflicts
  with the governance repository's documentation-only charter.
