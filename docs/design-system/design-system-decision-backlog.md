# Design System — Decision Backlog

**Status:** Living document
**Last Updated:** 2026-07-02
**Governs:** buzonas.dev Design System PRD §6 (Open Architectural Questions)

This is not an ADR and does not carry a version number in the sense CLAUDE.md §4 defines — it is a working list of the design system's remaining open architectural questions, expected to change shape as decisions are made and new ones surface. Per CLAUDE.md §8, nothing in this document is a decision. It exists to track what is still open and the context that bears on each question — not to pre-select an answer.

---

## Running Table

| # | Open Question | PRD Ref | Status | Context |
|---|---|---|---|---|
| 1 | How the brand guide is delivered — deployment topology and placement of brand-specific content | §3.1 | Deferred | **Deferred 2026-07-02** — delivery is a downstream brand implementation's concern, not forced upstream; revisit when a concrete brand implementation needs a delivery target. **Tooling is settled by ADR-019** — the brand guide is authored with Storybook Docs (MDX). What remains open is **delivery**, which is a downstream brand implementation's right, not forced upstream: whether a single theme-switching Storybook serves all brands versus per-brand deployments; and where brand-specific guide content lives (placing it in the brand-neutral core feels wrong, while standing up a guide per brand feels heavy). Weigh a possible upstream capability that packages brand guides so several coexist in one hosting solution. A delivery choice favoring a single combined deployment could require a new ADR superseding ADR-017's per-brand branding-site consequence. |
| 2 | The design-system ↔ portfolio component/theme split — what moves upstream vs stays site-local, and whether the site consumes the buzonas style package in place of its own `globals.css` / tailwind config | §3.5 (+ portfolio-PRD Phase 0 dependency) | Open | **Cross-cutting; no ADR.** The accepted portfolio docs assume the site owns `components/shared/*` and its own brand tokens (`component-inventory.md`; `portfolio-TAD.md`), while ADR-017/018 route the brand theme (buzonas style package) and shared components through the design system. `portfolio-PRD.md:292` makes the portfolio MVP build blocked on this system's Phase 1, so the coupling is live, not hypothetical. Resolving this likely also touches the portfolio governance docs. |
| 3 | What enforces the §4.2 hard gates (WCAG 2.1 AA, performance) at publish time, and the package-publish pipeline | §4.2 | Open | **Undecided.** ADR-020 covers GitHub Actions publishing the Storybook to Pages, but not the package-publish pipeline nor the mechanism that verifies the hard accessibility/performance gates "before release" (§4.2). Related to ADR-022's publishing tool (Changesets). |
| 4 | How a brand's theme is supplied to the core dev harness for local iteration without the core depending on the brand — the mechanism for §3.6 | §3.6 | Open | **Surfaced by ADR-024.** ADR-024 ships a brand-neutral reference theme and fixes a one-way brands→core dependency. The §3.6 capability's mechanism is open: e.g. an env-pointed external theme CSS file, a gitignored theme-drop directory the harness scans, or a linked local brand package discovered dynamically. A theme is a CSS-variable set (ADR-018), so file-injection may avoid package linking entirely. Touches item 2. |

---

## How This Gets Worked

Per CLAUDE.md §5/§8: one decision at a time, the owner decides, and the assistant presents alternatives and tradeoffs. When a question here is decided, it becomes a real ADR — numbered from whatever `docs/adr/` contains at that time — and is removed from this table rather than left duplicated in both places. New open questions are appended as they surface.

---

*Decision Backlog — buzonas.dev Design System*
*Not an ADR. Not versioned per CLAUDE.md §4. Reduced row-by-row as each question becomes a real, accepted ADR.*
