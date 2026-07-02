# Design System — Decision Backlog

**Status:** Living document
**Last Updated:** 2026-07-02
**Governs:** buzonas.dev Design System PRD §6 (Open Architectural Questions)

This is not an ADR and does not carry a version number in the sense CLAUDE.md §4 defines — it is a working list of the design system's remaining open architectural questions, expected to change shape as decisions are made and new ones surface. Per CLAUDE.md §8, nothing in this document is a decision. It exists to track what is still open and the context that bears on each question — not to pre-select an answer.

---

## Running Table

| # | Open Question | PRD Ref | Status | Context |
|---|---|---|---|---|
| 1 | How the brand guide is delivered — deployment topology and placement of brand-specific content | §3.1 | Open | **Tooling is settled by ADR-019** — the brand guide is authored with Storybook Docs (MDX). What remains open is **delivery**, which is a downstream brand implementation's right, not forced upstream: whether a single theme-switching Storybook serves all brands versus per-brand deployments; and where brand-specific guide content lives (placing it in the brand-neutral core feels wrong, while standing up a guide per brand feels heavy). Weigh a possible upstream capability that packages brand guides so several coexist in one hosting solution. A delivery choice favoring a single combined deployment could require a new ADR superseding ADR-017's per-brand branding-site consequence. |
| 3 | Where packages are published and under what namespace/scope | §3.5 | Open | **ADR-017 names the need but decides neither.** It flags a "publish/version workflow (registry access, versioning discipline) that did not previously exist," yet does not pick the registry (public npm vs GitHub Packages vs private) or the scope/name (e.g. `@buzonas/*` vs `@fancyguy/*`, cf. ADR-012's DBA). Consumability (§3.5) depends on this. No answer selected. |
| 4 | How component styles cross the package boundary — precompiled CSS in the package vs Tailwind source classes the consumer's build scans | §3.4, §3.5 | Open | **No ADR addresses this.** Interacts with the semantic-CSS-variable theme and the Tailwind v4 owned-source substrate (both ADR-018); the chosen mechanism must not require a consumer to modify or re-process components (§3.5). Largest unresolved technical unknown for distribution. |
| 5 | The server/client (RSC) contract for consumers of Radix-based components | §3.5 | Open | **No ADR addresses this.** Radix primitives are client-side (ADR-018); the buzonas.dev consumer is App Router / RSC (portfolio ADR-001). Whether components ship as `'use client'` and how that reconciles with a server-first consumer bears directly on consumability (§3.5). |
| 6 | The design-system ↔ portfolio component/theme split — what moves upstream vs stays site-local, and whether the site consumes the buzonas style package in place of its own `globals.css` / tailwind config | §3.5 (+ portfolio-PRD Phase 0 dependency) | Open | **Cross-cutting; no ADR.** The accepted portfolio docs assume the site owns `components/shared/*` and its own brand tokens (`component-inventory.md`; `portfolio-TAD.md`), while ADR-017/018 route the brand theme (buzonas style package) and shared components through the design system. `portfolio-PRD.md:292` makes the portfolio MVP build blocked on this system's Phase 1, so the coupling is live, not hypothetical. Resolving this likely also touches the portfolio governance docs. |
| 7 | Neutral-core vs theme-to-render — where the core Storybook's token sets come from, and the dependency direction between the core and brand style packages | §3.1, §3.4 | Open | **Apparent tension, no ADR.** ADR-017 says the core carries no buzonas-specific values, yet the docs shell (ADR-018/019) renders components under one or more brand themes. How a brand-neutral core demonstrates themed components — and whether the core depends on brand style packages — must be resolved before the TAD. |
| 8 | What enforces the §4.2 hard gates (WCAG 2.1 AA, performance) at publish time, and the package-publish pipeline | §4.2 | Open | **Undecided.** ADR-020 covers GitHub Actions publishing the Storybook to Pages, but not the package-publish pipeline nor the mechanism that verifies the hard accessibility/performance gates "before release" (§4.2). Related to ADR-022's publishing tool (Changesets). |

---

## How This Gets Worked

Per CLAUDE.md §8/§9: one decision at a time, the owner decides, and the assistant presents alternatives and tradeoffs. When a question here is decided, it becomes a real ADR — numbered from whatever `docs/adr/` contains at that time — and is removed from this table rather than left duplicated in both places. New open questions are appended as they surface.

---

*Decision Backlog — buzonas.dev Design System*
*Not an ADR. Not versioned per CLAUDE.md §4. Reduced row-by-row as each question becomes a real, accepted ADR.*
