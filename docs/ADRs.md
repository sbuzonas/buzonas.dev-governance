# Architectural Decision Records
## buzonas.dev — Personal Portfolio & Professional Brand Site

**Format:** MADR (Markdown Architectural Decision Records) — see [ADR-013](docs/adr/ADR-013-madr-format-for-decision-records.md)
**Status Legend:** Proposed | Accepted | Deprecated | Superseded
**Location:** Individual records live in [`docs/adr/`](docs/adr/), one file per decision. This page is the index only.

---

## Index

| ADR | Title | Status |
|-----|-------|--------|
| [ADR-001](docs/adr/ADR-001-nextjs-app-router-over-pages-router.md) | Next.js App Router over Pages Router | Accepted |
| [ADR-002](docs/adr/ADR-002-github-pages-for-hosting.md) | GitHub Pages for Hosting | Accepted |
| [ADR-003](docs/adr/ADR-003-shadcn-ui-as-component-library.md) | shadcn/ui as Component Library | Accepted |
| [ADR-004](docs/adr/ADR-004-mdx-for-long-form-content.md) | MDX for Long-Form Content | Accepted |
| [ADR-005](docs/adr/ADR-005-github-actions-for-ci-cd.md) | GitHub Actions for CI/CD | Accepted |
| [ADR-006](docs/adr/ADR-006-tailwind-css-for-styling.md) | Tailwind CSS for Styling | Accepted |
| [ADR-007](docs/adr/ADR-007-typescript-over-javascript.md) | TypeScript over JavaScript | Accepted |
| [ADR-008](docs/adr/ADR-008-next-mdx-over-next-mdx-remote.md) | @next/mdx over next-mdx-remote | Accepted |
| [ADR-009](docs/adr/ADR-009-staging-environment-deferred.md) | Staging Environment Deferred — GitHub Pages Limitation | Accepted |
| [ADR-010](docs/adr/ADR-010-svg-strategy-hybrid-inline-and-static.md) | SVG Strategy — Hybrid Inline and Static | Accepted |
| [ADR-011](docs/adr/ADR-011-repository-structure-single-repo-with-governance-companion.md) | Repository Structure — Single Repo with Governance Companion | Superseded in part (ADR-016) |
| [ADR-012](docs/adr/ADR-012-fancyguy-technologies-as-dba.md) | FancyGuy Technologies as DBA under buzonas.dev | Accepted |
| [ADR-013](docs/adr/ADR-013-madr-format-for-decision-records.md) | MADR Format for Decision Records | Accepted |
| [ADR-014](docs/adr/ADR-014-blog-slug-route-deferred.md) | Blog Dynamic Route (`/blog/[slug]`) Deferred Until First Post — Next.js Static Export Limitation | Accepted |
| [ADR-015](docs/adr/ADR-015-consulting-architectures-slug-deferred.md) | Consulting Architecture Route (`/consulting/architectures/[slug]`) Deferred — Same Root Cause as ADR-014 | Accepted |
| [ADR-016](docs/adr/ADR-016-governance-submodule-in-site-repo.md) | Governance Repo Included as Git Submodule in Site Repo | Accepted |
| [ADR-017](docs/adr/ADR-017-design-system-repository-and-distribution-topology.md) | Design System Repository & Distribution Topology | Accepted |
| [ADR-018](docs/adr/ADR-018-design-system-component-substrate-and-theme-mechanism.md) | Design System Component Substrate & Theme Mechanism | Accepted |
| [ADR-019](docs/adr/ADR-019-storybook-as-component-workshop-and-docs.md) | Storybook as the Design System Component Workshop & Documentation Tool | Accepted |
| [ADR-020](docs/adr/ADR-020-design-system-storybook-hosting-on-github-pages.md) | Design System Storybook Hosting on GitHub Pages | Accepted |

---

## Adding a New ADR

1. Determine the next sequential number (per ADR-011/this domain's numbering — see CLAUDE.md for the governance repo's conventions).
2. Create `docs/adr/ADR-NNN-short-title-slug.md` using the MADR structure: Context, Decision, Rationale, Consequences, Alternatives Considered.
3. Add a row to the index table above, in numeric order.
4. If the new ADR supersedes or amends an existing one, update that ADR's **Status** to `Superseded` or `Deprecated` and add a note linking to the replacement — do not delete or silently rewrite the original.

---

*ADR Index v1.1 — buzonas.dev Portfolio Site*
*Individual records: [`docs/adr/`](docs/adr/)*
