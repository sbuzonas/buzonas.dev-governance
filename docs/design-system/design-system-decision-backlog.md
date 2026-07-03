# Design System — Decision Backlog

**Status:** Living document
**Last Updated:** 2026-07-03
**Governs:** buzonas.dev Design System PRD §6 (Open Architectural Questions)

This is not an ADR and does not carry a version number in the sense CLAUDE.md §4 defines — it is a working list of the design system's remaining open architectural questions, expected to change shape as decisions are made and new ones surface. Per CLAUDE.md §8, nothing in this document is a decision. It exists to track what is still open and the context that bears on each question — not to pre-select an answer.

---

## Running Table

| # | Open Question | PRD Ref | Status | Context |
|---|---|---|---|---|
| 1 | How the brand guide is delivered — deployment topology and placement of brand-specific content | §3.1 | Deferred | **Deferred 2026-07-02** — delivery is a downstream brand implementation's concern, not forced upstream; revisit when a concrete brand implementation needs a delivery target. **Tooling is settled by ADR-019** — the brand guide is authored with Storybook Docs (MDX). What remains open is **delivery**, which is a downstream brand implementation's right, not forced upstream: whether a single theme-switching Storybook serves all brands versus per-brand deployments; and where brand-specific guide content lives (placing it in the brand-neutral core feels wrong, while standing up a guide per brand feels heavy). Weigh a possible upstream capability that packages brand guides so several coexist in one hosting solution. A delivery choice favoring a single combined deployment could require a new ADR superseding ADR-017's per-brand branding-site consequence. |
| 2 | Classification of the low-complexity shared primitives — a mono "tech tag" chip, a section "eyebrow" overline, and a status badge: core-owned generic elements vs site-local | §3.5 | Deferred | **Deferred 2026-07-02, from ADR-027.** ADR-027 settled the design-system↔portfolio boundary (site consumes the core; buzonas is brand #1) but left these three primitives site-local and unclassified: it is not yet clear they are worth decomposing into reusable elements, nor whether they are generic rather than brand-specific. Revisit when their reuse value is clearer — a generic base may then move to the core while any portfolio-specific vocabulary (e.g. the project-status set) stays site-local. |
| 4 | Functional / interaction / unit testing approach for components, beyond the accessibility and performance verification already fixed by ADR-030 | §3.2 | Deferred | **Deferred 2026-07-03.** ADR-030 settled the release-gate content — accessibility (axe + recorded manual review) and performance (L0/L1/L2) — and ADR-029 lists `test` among the required PR-time status checks, but no decision fixes a functional/interaction/unit testing framework or expectation. ADR-019 already deferred interaction and visual-regression testing as out of initial scope. Kept deferred and note-only, consistent with ADR-019's scope discipline: the TAD records it as minimal/deferred rather than resolving it. Revisit if functional coverage becomes warranted (e.g. as component complexity grows beyond thin Radix wrappers). |

---

## How This Gets Worked

Per CLAUDE.md §5/§8: one decision at a time, the owner decides, and the assistant presents alternatives and tradeoffs. When a question here is decided, it becomes a real ADR — numbered from whatever `docs/adr/` contains at that time — and is removed from this table rather than left duplicated in both places. New open questions are appended as they surface.

---

*Decision Backlog — buzonas.dev Design System*
*Not an ADR. Not versioned per CLAUDE.md §4. Reduced row-by-row as each question becomes a real, accepted ADR.*
