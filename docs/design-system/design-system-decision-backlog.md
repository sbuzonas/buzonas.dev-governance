# Design System — Decision Backlog

**Status:** Living document
**Last Updated:** 2026-07-01
**Governs:** buzonas.dev Design System PRD v1.0, §6 (Open Architectural Questions)

This is not an ADR and does not carry a version number in the sense CLAUDE.md §4 defines — it is a working list, expected to change shape as decisions are made and new questions surface. Per CLAUDE.md §8, nothing in this document is a decision. It exists to track what's still open, what background exists for each question, and what previously-drafted material is available to draw alternatives from — not to pre-select an answer.

Four ADR-numbered drafts were produced before this PRD was accepted, in violation of the PRD → ADR → TAD sequence CLAUDE.md §8 now documents. They have been retired: **the numbers 017–020 are released** — no ADR in the real `docs/adr/` sequence has ever reached them, so the next real ADR takes 017 whenever it's actually decided. The retired drafts are kept below, in an appendix, as background material only — their Context and Alternatives Considered sections contain research worth reusing; their Decision sections do not carry any authority and should not be treated as a starting default.

---

## Running Table

| # | Decision Point | PRD §6 Ref | Status | Background Available | Validity of Retired Draft's Conclusion |
|---|---|---|---|---|---|
| 1 | Where does the design system live (repository/location) | Q1 | Open | Former draft ADR-017 (Appendix A) | Alternatives (new repo / folder in site / folder in governance / defer) are still reasonable starting alternatives. The specific repo name used was always illustrative, not a real proposal. |
| 2 | What tool is used for component development and live documentation | Q2 | Open | Former draft ADR-018 (Appendix B) | Alternatives (Storybook, Ladle, custom-built, continued reliance on Canva AI generation) are still reasonable starting alternatives. **Must be re-evaluated against the theme-agnostic component requirement (PRD §3.4)** — the retired draft's reasoning never considered that requirement, since it was written before the PRD's theming decision existed. Don't treat "Storybook" as pre-selected. |
| 3 | What mechanism implements the theme layer | Q3 | Open | Former draft ADR-019 (Appendix C) | **Conclusion does not carry over.** The retired draft's entire premise — "buzonas.dev is the source of truth for token values, the design system mirrors them" — assumes there is one fixed token set to mirror. PRD §3.4 requires components to be theme-agnostic, with buzonas.dev's identity as only the *first* plugged-in theme among potentially several. This is a different question than the one the retired draft answered. The submodule-access research (how a repo reads another repo's files, given tooling constraints) may still be relevant background, but the "site owns the tokens" framing needs to be rederived, not patched. |
| 4 | How a consuming project actually obtains/integrates components | Q4 | Open | Former draft ADR-019 (Appendix C, partial) | The retired draft only addressed *token* access via git submodule — it never addressed component distribution generally (copied source vs. published package vs. submodule vs. other). This is a broader question than what was previously drafted; treat as unaddressed rather than partially answered. |
| 5 | Where this system's documentation and decision history is governed | Q5 | Open | Former draft ADR-017 (Appendix A, touched on briefly) | The retired draft asserted governance folds into the existing `buzonas.dev-governance` repo, citing the "unified governance domain" principle, but did not seriously weigh alternatives before concluding. Treat as open; the reasoning is worth re-examining, not the conclusion. |
| 6 | Deployment/hosting target for the built artifact | Q6 | Open | Former draft ADR-018 (Appendix B, touched on briefly) | Mentioned in passing (GitHub Pages vs. Chromatic) without real alternatives analysis, and downstream of whichever tool is chosen for Q2. Treat as fully open until Q2 is settled. |
| 7 | How live brand-reference documentation (PRD §3.1) is actually delivered | Feeds §3.1 | Open | Former draft ADR-020 (Appendix D) | This was framed as its own decision ("Storybook Docs/MDX") but is really a sub-question of Q2 — whatever tool is chosen must satisfy §3.1's live-rendering requirement, and *how* it does so follows from that choice rather than being independently decided first. |

---

## How This Gets Worked

Per CLAUDE.md §8/§9: one decision at a time, owner decides, assistant presents alternatives and tradeoffs. Question 1 (repository location) is the natural starting point since several others (5, and partially 6) are downstream of it. Question 3 (theme mechanism) is the one most worth taking slowly — it's the most consequential and the one where the retired draft's thinking is least reusable as-is.

When a row here gets decided, it becomes a real ADR (numbered from whatever `docs/adr/` actually contains at that time) and is removed from this table, not left duplicated in both places.

---

## Appendix: Retired Draft Material (Reference Only — Not ADRs)

The following four documents were originally drafted as ADR-017 through ADR-020. They are reproduced here verbatim for their research value (Context and Alternatives Considered sections) — their Decision, Rationale, and Consequences sections do not reflect an accepted decision and should not be read as one.

### Appendix A — Formerly "ADR-017: Separate Repository for the Design System / Component Playground"

**Date:** 2026-06-30

#### Context (as originally drafted)

The buzonas.dev brand identity work produced a hand-built HTML/SVG draft (`brand-guidelines-showcase.html`) as a proof that real brand values render correctly. That draft has no component reuse story and isn't a maintained tool. A decision is needed on where the living, maintained version — brand reference plus component playground — should live.

#### Alternatives Considered (as originally drafted)

- **New, separate repository** — repo-scoping philosophy (independent toolchain, independent release cadence, reusability beyond a single site) was the reasoning offered for this option.
- **Folder within `buzonas.dev`** — would couple the component playground's release cadence to the site's.
- **Folder within `buzonas.dev-governance`** — governance repo holds planning artifacts, not a running application/tool with its own build pipeline; potential mismatch in repo purpose.
- **Wait until after portfolio launch to decide** — the original plan, logged as a Future Initiative before this line of work was picked up early.

---

### Appendix B — Formerly "ADR-018: Storybook as the Component Development Environment"

**Date:** 2026-06-30

#### Context (as originally drafted)

The design system needs a tool for developing and documenting UI components in isolation, paired with a living brand reference. AI design-generation tooling (Canva's `generate-design` and Magic Write) was evaluated against this use case and found unreliable — `generate-design` against `doc` type produced text-only output with bracketed placeholders instead of rendered graphics; against `report` type it produced real graphics but contaminated with an unrelated fabricated financial-report template. Magic Write collapsed a structured color palette into a text table when asked to update it. A hand-built HTML/SVG draft proved brand values could render correctly but is not a maintained, reusable tool.

#### Alternatives Considered (as originally drafted)

- **Storybook** — cited for MDX Docs support, native React/Tailwind/shadcn integration, and a Controls addon for live prop adjustment.
- **Continue hand-building static HTML** — no component reuse story, no live prop adjustment, doesn't scale as a maintained tool.
- **Continue relying on Canva AI generation** — rejected based on direct testing; both tools fabricated content or dropped requested detail even with explicit prompts.
- **Ladle** — noted as a lighter-weight Storybook alternative, flagged as having less mature Docs/MDX support at evaluation time.
- **Custom-built tool** — would reinvent a well-solved problem.

---

### Appendix C — Formerly "ADR-019: Design Token Source of Truth (Interim)"

**Date:** 2026-06-30

#### Context (as originally drafted)

Brand tokens existed in three places: the site's `globals.css`/`tailwind.config.ts`, the brand guide documentation, and (once built) the design system repository. A decision was framed as needed on which is authoritative and how the others stay in sync.

**Note on why this framing is now considered invalid:** this Context assumes a single authoritative token set that other locations "sync" against. PRD v1.0 §3.4 instead requires components to be theme-agnostic, with the buzonas.dev identity as one pluggable theme among potentially several — there is no longer a single "the tokens" for one location to own.

#### Alternatives Considered (as originally drafted)

- **Site remains source of truth, design-system repo mirrors via git submodule** — the mechanism reasoning (Claude Code cannot fetch arbitrary URLs, only read local files, per the precedent already established for governance-doc access in the site repo) may still be relevant to *how* a theme's values get physically accessed by tooling, independent of whether "site owns the tokens" still holds as a framing.
- **Design-system repo becomes the source of truth, site consumes from it** — would require site changes with no pain point driving it at the time.
- **Publish a shared package immediately** — considered premature; no second consumer existed yet.
- **Copy token values directly (no submodule)** — flagged as recreating a duplication/divergence failure mode already rejected elsewhere in this project's history.
- **Reference tokens by URL** — flagged as inert for tooling that can't fetch arbitrary URLs.

---

### Appendix D — Formerly "ADR-020: Brand Reference and Component Stories Live Together (Storybook Docs)"

**Date:** 2026-06-30

#### Context (as originally drafted)

The hand-built HTML draft demonstrated value in placing brand reference material directly alongside rendered examples rather than as a separate cross-referenced document.

#### Alternatives Considered (as originally drafted)

- **Storybook Docs/MDX pages, same tool and navigation as component stories** — the option originally selected; reasoning included MDX's ability to embed live components directly in documentation prose.
- **Keep the HTML draft as the long-term brand reference, separate from whatever tool is chosen** — flagged as recreating a two-artifacts-drift-apart problem already observed once in this project's history.
- **A separate static docs site** — flagged as duplicating what a Docs-capable component tool would already do, pending which tool is actually chosen.

---

*Decision Backlog — buzonas.dev Design System*
*Not an ADR. Not versioned per CLAUDE.md §4. Superseded row-by-row as each decision point becomes a real, accepted ADR.*
