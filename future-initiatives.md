# Future Initiatives — buzonas.dev Project Family

Lightweight parking lot for ideas that clear the bar for future dedicated planning but aren't being acted on yet. Revisit after the portfolio site ships (v1.0.0).

---

## Component Library & Storybook

**Status:** ~~Noted, not scoped~~ **Promoted to real planning artifacts (2026-06-30); active as of 2026-07-01.** See `docs/design-system/design-system-PRD.md` and `docs/design-system/design-system-decision-backlog.md`. No longer waiting for post-portfolio-launch — formalized now; buzonas.dev's own PRD (Phase 0) now gates MVP build on this being finalized first.

**The idea:** Isolate shared UI components (currently living in `buzonas.dev`'s `/components/ui/` as owned shadcn/ui source) and design tokens (Tailwind config, CSS custom properties) into their own repository, with Storybook as the development/documentation environment — a living component playground paired with the brand reference, similar to the HTML brand-guidelines showcase built during Canva Brand Kit work (2026-06-30).

**Why this likely warrants its own PRD + repo, not a phase entry in the existing portfolio PRD:**
- Different audience — the portfolio PRD serves hiring managers/recruiters; a component library serves future-you building other things faster
- Outlives the portfolio — natural toolkit for FancyGuy consulting deliverables, Veterans Corner, and future projects, not scoped to buzonas.dev's job-seeking timeline
- Independent release cadence and distinct toolchain (Storybook build/test/deploy vs. Next.js static export) — both are explicit repo-scoping triggers already established in ADR-011

**Active:** PRD accepted and architectural decisions in progress via `docs/design-system/design-system-decision-backlog.md`.
