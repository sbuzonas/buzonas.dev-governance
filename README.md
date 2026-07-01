# buzonas.dev-governance

Project governance and planning documentation for **[buzonas.dev](https://buzonas.dev)** — Steve Buzonas's personal portfolio and professional brand site.

This repository is the single source of truth for *why* buzonas.dev is built the way it is. It exists separately from the application source ([`sbuzonas/buzonas.dev`](https://github.com/sbuzonas/buzonas.dev)) because these are reference artifacts consumed by humans and AI coding assistants — they have no build dependency on the site itself, and they're a portfolio signal in their own right.

## Why a Separate Repository

Per [ADR-011](docs/ADRs.md#adr-011), application code, content, and structured data are co-located with the site (they share a build pipeline and deploy target). Governance documents don't share that pipeline — they're planning and decision artifacts, not build inputs. Separating them lets this repo serve as a standalone context source: an AI coding assistant working in the site repo reads `CLAUDE.md` here before generating any code, without governance docs cluttering the application tree.

## Documents

| Document | Purpose |
|----------|---------|
| [`portfolio-PRD.md`](docs/portfolio/portfolio-PRD.md) | Product Requirements Document — purpose, audiences, section specs, release phases |
| [`portfolio-TAD.md`](docs/portfolio/portfolio-TAD.md) | Technical Architecture Document — stack, repo structure, routing, CI/CD pipeline design |
| [`ADRs.md`](docs/ADRs.md) | Index of Architectural Decision Records — links to individual records in [`docs/adr/`](docs/adr/) |
| [`docs/adr/`](docs/adr/) | Individual MADR-format decision records, one file per decision |
| [`buzonas-brand-guide.md`](buzonas-brand-guide.md) | Brand identity — color system, typography, signature visual elements, voice & tone |
| [`component-inventory.md`](component-inventory.md) | Full page and component specification — props, data sources, MVP status |
| [`CLAUDE.md`](CLAUDE.md) | AI context for working **in this repository** — document conventions, ADR process, relationship to the site repo |

`CLAUDE.md` is the entry point if you're an AI assistant or a human picking up this repository cold. Note that it describes how to work with *these governance documents* — the buzonas.dev application's own implementation contract (stack constraints, component rules) lives in [`buzonas.dev`'s own `CLAUDE.md`](https://github.com/sbuzonas/buzonas.dev/blob/main/CLAUDE.md), which is derived from the documents here but is not duplicated in this repository.

## Scope

This repository governs the `buzonas.dev` portfolio site and is the unified governance domain for that site and any in-scope subprojects built under it. It does not cover unrelated personal ventures (e.g., the Veterans Corner nonprofit), which maintain their own independent repositories and governance per the case-by-case philosophy in ADR-011.

## Relationship to the Site Repository

| | This repo | [`buzonas.dev`](https://github.com/sbuzonas/buzonas.dev) |
|---|---|---|
| Contains | Planning, decisions, brand spec, AI context | Application code, content, structured data |
| Builds / deploys | No | Yes — Next.js static export to GitHub Pages |
| Updated when | A decision, requirement, or design changes | A decision is implemented |

Changes here are typically made *before* a corresponding implementation change in the site repo — this is documentation-first by design (see PRD §1.2, ADR-013).

## License

All content in this repository is **All Rights Reserved** — see [`LICENSE`](LICENSE). Unlike the site repository's source code (MIT licensed), these are planning and brand artifacts with no standalone utility outside this project's context, and are not licensed for reuse.

## Maintainer

Steve Buzonas — [buzonas.dev](https://buzonas.dev) · [LinkedIn](https://linkedin.com/in/sbuzonas) · [GitHub](https://github.com/sbuzonas)
