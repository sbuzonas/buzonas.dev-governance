# ADR-030: Design System Release-Gate Content — Accessibility & Performance Verification

**Date:** 2026-07-03
**Status:** Accepted

## Context

ADR-029 fixed the package-publish pipeline (a human-gated Changesets release-PR, trunk-based batch)
and *reserved* a required pre-publish release-gate stage, deliberately leaving its content to a
separate decision. This record fills that stage: what accessibility and performance verification runs,
at what cadence, and with what enforcement, before a release publishes to npm.

The requirement is PRD §4.2 (release gate for consumable components): released components must meet
WCAG 2.1 AA and must not be the cause of a consuming project failing its own performance budget,
*verified before release, not assumed*. PRD §4.1 (soft gates while iterating) is the counterpart —
accessibility and performance are not enforced on components still being prototyped, only on released
ones. §4.2 also states that the specific measurable performance threshold is an implementation detail,
while the *principle* — released components carry their share of a known downstream bar — is the
requirement.

Two facts bound what a "hard gate" can contain. First, a hard gate must be objective and run before
the irreversible npm publish (ADR-029). Second — the fact that shapes the whole decision —
**automated checks are a floor, not the full bar**: automated accessibility tooling (axe) detects only
a portion of WCAG 2.1 AA and is blind to keyboard operability, focus order, meaningful sequence, and
semantic correctness; and "performance" for a *library* is not a page score but the weight and
structure it contributes to a consumer. The prior omission of any component benchmarking — on the
grounds that this is "a library" — is precisely the abdication this decision corrects: deficiencies
not caught at design time are rarely reported back by downstream consumers, who work around them
locally instead.

Relevant prior decisions: components live in Storybook (ADR-019); they are built on Radix + Tailwind
with semantic CSS-variable theming (ADR-018); the core renders against a brand-neutral reference theme
(ADR-024); style delivery is scan-first/prebuilt with a Preflight-free constraint (ADR-025); and the
RSC contract keeps client boundaries granular (ADR-026). The portfolio site (buzonas.dev) is the first
consumer, built in tandem, and already runs `lhci` and `axe` in its own CI.

## Decision

The release-gate content is a **graduated verification regime** — cheap-and-continuous at commit,
representative-and-visible at PR, representative-and-enforced at release — applied to *released*
components (§4.2), not to components still being prototyped (§4.1).

### Accessibility

- **Automated (pre-publish gate):** the Storybook test-runner runs **axe** against each released
  component's stories in CI, scoped to the WCAG 2.1 AA ruleset, and must report zero violations. The
  Storybook a11y addon provides the same engine as live authoring feedback in the dev loop (soft,
  §4.1).
- **Manual review — hybrid:** a recorded manual accessibility review (keyboard operability, focus
  order, screen-reader spot-check) is required for **new or changed** components at the release that
  first ships them; unchanged components rely on automated axe for regression. The accessibility hard
  gate is therefore **"automated axe green *and* a recorded manual review sign-off on the release PR"**
  for new/changed components — extending "hard gate" from a CI green-check to a green-check plus a
  required human review, consistent with ADR-029's human-in-the-release-loop stance.
- **Theme/contrast boundary:** the core gate owns component-level AA (semantics, ARIA, keyboard, focus)
  **plus reference-theme contrast**. Color-contrast (WCAG 1.4.3) is partly a theme property; a specific
  brand's theme contrast is the **brand/consumer's** responsibility, verified where the brand theme is
  authored (the ADR-028 harness) or in a consumer — not by the brand-neutral core. This mirrors the
  theme-agnostic-core principle (ADR-024).

### Performance

A layered measurement regime, so the library's impact is measured at design time rather than deferred
to downstream:

- **L0 — Structural (build-time, always):** the ADR-025 Preflight-free / no-duplicate-utilities
  properties, ADR-026's minimal client boundaries, and tree-shakeable ESM, asserted at build.
  Guardrails, not measurement.
- **L1 — Weight budgets (every commit):** bundle-size measurement on published output, **enforced** —
  a budget breach blocks — and **reporting per-change deltas** so long-term drift is visible (trend),
  even when a change stays under an absolute budget.
- **L2 — Reference-consumer E2E (deferred; see trajectory):** a minimal, brand-neutral,
  reference-themed consumer application that installs and uses the components as a real downstream
  project would (via ADR-025's paths), measured end-to-end (Lighthouse for performance, reused for E2E
  axe). **Informative (non-blocking) on PRs, a hard gate on release.**

### Deferral trajectory (L2 / the reference app)

The synthetic reference consumer app is **deferred until a second, differently-branded consumer
exists** (PRD Phase 3). While there is a single consumer and it is the owner's own tandem project
(buzonas.dev), that real consumer *is* the representative surface — a synthetic app would measure a
fiction. The moment a second differently-branded consumer exists, no single real consumer represents
the others, and a brand-neutral synthetic surface becomes necessary (and keeps the core's own gate
brand-clean, ADR-024).

In the interim:
- The **formal pre-publish performance hard-gate is L1** (weight budgets) in the design system's own
  CI.
- **buzonas.dev is the richer feedback loop** — tandem-development observation plus its existing
  post-publish `lhci`/`axe` CI — providing interim integration performance and accessibility signal,
  and standing as living §3.5 proof (a real consumer consuming the packages unmodified). This is
  acceptable *because buzonas.dev is the owner's own tandem project* (the "downstream never reports"
  failure does not apply to oneself); it would not substitute for a pre-publish gate against
  third-party consumers, which is why the synthetic pre-publish L2 remains the eventual target.

### Cadence summary

| Layer | Commit | PR | Release |
|---|---|---|---|
| L0 structural | always | always | always |
| L1 weight budgets | enforced + delta | carried | enforced (interim perf hard-gate) |
| L2 reference-app E2E | — | informative | hard gate *(once the app exists)* |
| axe per released story | — | feedback (soft for prototypes, §4.1) | enforced (zero AA violations) |
| Manual a11y review | — | — | required + recorded for new/changed |

### Left as implementation detail

Specific budget values and score thresholds (§4.2 names these implementation details); the exact axe
configuration and test-runner wiring; the story convention that marks a component "released" vs.
"prototype" for gate scoping; and the manual-review record's exact form (checklist location, sign-off
mechanism) on the release PR.

## Rationale

- **Automated-as-floor is made explicit, not pretended away:** axe green is necessary but not
  sufficient for AA, so the hybrid adds recorded manual review exactly where the non-automatable half
  of AA lives, and only for new/changed components — catching issues at design time where introduced,
  without re-auditing unchanged components each batch. Cost is proportional to new/changed surface.
- **Performance is measured, not waved off as "it's a library":** L1 gives continuous, enforced,
  trend-visible weight budgeting from the first commit; L2 gives representative consumer-altitude
  measurement once a synthetic surface is warranted. Together they catch the library's downstream
  impact upstream, which is the §4.2 spirit.
- **The graduated cadence honors §4.1/§4.2:** exploration is never blocked (soft at commit/PR for
  prototypes), while released components are enforced before an irreversible publish.
- **The deferral is a trajectory, not a gap:** L1 holds the pre-publish perf line immediately; the
  real tandem consumer supplies richer interim signal; the synthetic app formalizes and
  brand-neutralizes that signal when a second consumer makes it necessary — matching the second-brand
  milestone rather than an arbitrary date.
- **The contrast/theme boundary keeps the core brand-clean (ADR-024):** the core cannot promise
  contrast conformance for brand themes it never sees; it owns structure + reference-theme contrast,
  and brands own their theme's contrast.

## Consequences

- **Positive:** §4.2 is verified before release from day one (L1 + axe + manual for new/changed);
  performance impact is visible continuously with trend; the model scales to multiple consumers and
  upgrades cleanly to a synthetic pre-publish surface without rework.
- **Positive:** the accessibility gate is honest about its floor and closes the non-automatable gap
  with proportional manual review, rather than shipping unverified AA.
- **New / cost:** a recorded manual a11y review is required for new/changed components at release (a
  process step on the release PR, not a CI check); weight budgets and axe wiring must be stood up; the
  deferred reference app is future build-out.
- **Constraint / boundary:** the core does not verify brand-theme contrast; consumers/brands own that.
  Deferring L2 means integration-level performance and accessibility are interim-verified post-publish
  via buzonas.dev, not pre-publish, until the second-brand trigger.
- **Handoff:** this completes the release-gate content ADR-029 reserved; specific thresholds and wiring
  remain implementation details for the repository.

## Alternatives Considered

- **Automated-only accessibility gate.** Fully CI-enforceable and zero manual burden, but silently
  redefines AA down to the axe-detectable subset and ships keyboard/focus/semantic issues unverified —
  the exact downstream-discovers-it failure this decision rejects. Declined.
- **Manual review every release for all released components.** Most thorough, but re-audits unchanged
  components every batch — recurring cost disproportionate to the actual change surface and to a
  small/solo maintainer. Declined in favor of the new/changed-scoped hybrid.
- **Page-score performance gate via Lighthouse on Storybook (or no perf gate at all).** Measuring
  Storybook conflates workshop/addon overhead with component cost; "no perf gate because it's a
  library" is the abdication §4.2 was reframed to correct. Declined in favor of weight budgets now and
  a representative consumer surface later.
- **Build the synthetic reference app now.** Rejected as premature: with a single tandem consumer, the
  real consumer is the representative surface and a synthetic app would measure a fiction; the
  second-brand trigger is when it stops being redundant.
- **Core owns brand-theme contrast.** Rejected: the brand-neutral core never sees brand themes
  (ADR-024); promising their contrast conformance is a guarantee it cannot keep.
