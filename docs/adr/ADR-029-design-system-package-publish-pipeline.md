# ADR-029: Design System Package-Publish Pipeline — Human-Gated Changesets Release-PR, Trunk-Based Batch

**Date:** 2026-07-03
**Status:** Accepted

## Context

The design system publishes its core packages to npm. ADR-022 fixed the tooling — pnpm workspaces,
Turborepo, and Changesets on GitHub Actions, with independent per-package versioning — and ADR-023
fixed the target — the public npm registry under the `@sbuzonas` scope. Both deliberately left the
*shape of the release workflow* as an open question: ADR-022 called the exact CI release workflow an
implementation detail, and ADR-023 un-gated first publish without saying how releases are cut.
ADR-025 added multiple build outputs the pipeline must produce and release (scannable source, a
Tailwind convenience configuration, and a prebuilt Preflight-free CSS bundle). ADR-020 established a
*separate* pipeline that deploys the Storybook docs to GitHub Pages; this record concerns the
distinct **package** publish to npm.

What remained open is the release flow itself: the relationship between merging code and cutting a
release, where the human decision sits, and where release-time verification attaches before the
irreversible npm publish. PRD §4.2 (release gate for consumable components) requires released
components to meet hard accessibility and performance gates *before release*, which presupposes a
defined release process with a pre-publish checkpoint; §3.5 (consumability without modification)
requires versioned packages a consumer installs as-is; and §1.2/§4.3 (independent cadence, standalone
artifact) require releasing on the system's own schedule, decoupled from any consumer.

The governing forces are the owner's stated engineering principles: a continuous-delivery bias; an
aversion to unintended or premature releases of an irreversible, publicly consumed artifact; ceremony
proportioned to blast radius and a likely-growing contributor surface; and **automate every mechanic
but never remove the human from the release decision.**

## Decision

- **Release via a Changesets release-PR on GitHub Actions.** Feature PRs each carry a changeset
  (ADR-022) declaring release intent and the semver bump. The Changesets action maintains a standing
  "Version Packages" PR that aggregates all pending changesets. **Merging that PR is the sole human
  release act**; it triggers the automated version bump, changelog, tag, and `changeset publish` to
  public npm under `@sbuzonas` (ADR-023).
- **Trunk-based batch-all semantics.** A release assembles *all* changesets accumulated on `main`
  since the previous release into one versioned unit — a release is a curated, assembled set approved
  for consumption, not a per-feature echo. The curation lever is *timing*: the maintainer chooses when
  to merge the release PR.
- **Human-in-the-loop by construction.** Every mechanic is automated; the human's only decision is
  whether and when to merge the release PR, made against the version-and-changelog preview that PR
  presents.
- **A pre-publish release-gate stage guards the point of no return.** Because an npm publish is
  effectively irreversible, a release cannot publish until the release-gate checks are green. This
  record *names and reserves* that stage as a required checkpoint on the release PR; the checks it
  enforces and their thresholds — the §4.2 accessibility and performance gates — are a **separate
  decision and are not fixed here.**
- **The publish step produces ADR-025's full entry-point set** — scannable source, the Tailwind
  convenience configuration, and the prebuilt Preflight-free CSS bundle — via the Turborepo build
  (ADR-022) before publishing.
- **Publishing uses npm provenance via GitHub OIDC**, consistent with the public, inspectable posture
  of ADR-023 and the automate-when-possible principle.
- **PR-time CI is separate from the release workflow.** Build, typecheck, lint, test, and the
  release-gate checks run as required status checks (including on the release PR); the release workflow
  performs only version-and-publish.

Explicitly not adopted now: the **release-branch / integration-branch topology** (selecting a *subset*
of landed work, octopus-style candidate assembly) is a **later evolution**, not part of this decision.
The exact workflow YAML, action versions, and any remote-cache backend remain implementation details
(ADR-022).

## Rationale

- The changeset already captures release intent and semver at PR time (ADR-022), so the residual
  question is only *when accumulated intents execute*. A standing release PR turns that execution into
  a single, well-informed, human-approved act — satisfying "automate the mechanics, keep the human on
  the release" without any manual version or tag work.
- A required-green gate on the release PR is the strongest natural pre-publish checkpoint, directly
  serving §4.2's "verified before release" against an irreversible publish — a stronger guard than the
  reversible docs deploy (ADR-020) needs.
- Trunk-based batch-all is the simplest model that already yields a curated, assembled release unit; it
  fits a solo maintainer today and imposes no branch-topology overhead.
- The model is chosen partly for its **clean upgrade path**: the same release-PR flow extends to a
  release-branch/integration topology (subset curation, octopus-style assembly) if and when parallel
  contributor streams warrant it — a robustness gain available later *without re-architecting the
  release flow*. That forward compatibility is a deliberate driver of picking this model now rather
  than a heavier one.
- Provenance is a low-cost supply-chain signal that fits the public/showcase posture and the
  automate-when-possible principle.
- Keeping the package publish distinct from ADR-020's docs deploy preserves two pipelines with
  distinct triggers and targets, each releasing on its own terms.

## Consequences

- **Positive:** releases are deliberate, batched, and human-approved, with every mechanic automated;
  the reserved pre-publish gate gives §4.2 a concrete attachment point; the model scales to multiple
  contributors and to a richer release-branch topology without rework.
- **Positive:** consumers install versioned, provenance-attested packages from public npm with zero
  configuration (ADR-023).
- **New / cost:** the standing "Version Packages" PR is a persistent artifact to maintain and merge;
  releases accumulate until the maintainer merges it (intended — curation by timing); the publish job
  needs npm publish authorization (OIDC/provenance) and the bot needs PR-write.
- **Constraint / handoff:** a release cannot publish with a red gate, but the gate's *content* is
  unresolved until the separate release-gate decision lands — so first *gated* publish depends on that
  decision (the publish mechanics themselves do not). This record defines the pipeline and reserves the
  stage; a separate decision fills it with the §4.2 accessibility and performance checks and their
  thresholds.
- **Deferred / unaffected:** the release-branch/integration topology (later evolution); the exact YAML,
  action pins, and remote-cache backend (implementation details, ADR-022); ADR-018's deferred
  token-generation source (unaffected).

## Alternatives Considered

- **Publish-on-merge (guarded continuous delivery).** Maximal automation and CD flow, but it fuses the
  release into the code-merge; even with a GitHub Environment approval bolted onto the publish job, the
  human's release decision is less informed than a release PR's version-and-changelog preview, and it
  thins the "keep the human on the release" principle. Rejected; the CD-latency it avoids is precisely
  the batching this decision treats as a feature.
- **Tag / manual-dispatch publish.** Preserves a deliberate human release act but automates the least —
  manual version and tag discipline, no rolling changelog preview — cutting against the
  automate-when-possible principle. Rejected; remains the fallback if the standing release PR ever
  proves not worth its keep.
- **Release-branch / integration topology now** (subset curation, octopus-style assembly). The more
  robust end state, but premature at solo scale: it adds branch topology and coordination cost for
  selection power not yet needed. Deferred as the later evolution this model upgrades into, not the
  starting point.
