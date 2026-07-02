# CLAUDE.md
## AI Context — buzonas.dev-governance Repository

This file is the entry point for any AI assistant working **in this repository**. It is not the site's coding context — for stack constraints, component rules, and implementation guardrails for the buzonas.dev application itself, see [`buzonas.dev`'s `CLAUDE.md`](https://github.com/sbuzonas/buzonas.dev/blob/main/CLAUDE.md), which inherits its requirements from the documents stored here.

If you arrived here while working on application code, you're in the wrong repo for that task — this repository contains no buildable source. Read this file to understand what *is* here and how to work with it correctly.

---

## 1. What This Repository Is

`buzonas.dev-governance` is the planning, decision-record, and brand-specification companion to the `buzonas.dev` portfolio site. Per [ADR-011](docs/adr/011-repository-structure-single-repo-with-governance-companion.md), it is intentionally separate from the application repository because these documents have no build dependency on the site and serve as a standalone reference — for the developer, for hiring managers evaluating engineering discipline, and for AI coding assistants that need authoritative context before generating code.

This repository is **documentation only**. There is no `package.json`, no build pipeline, no application code. Do not propose adding any.

## 2. What's In Scope

This repository governs the `buzonas.dev` portfolio site and any future in-scope subproject built under that domain. It does not cover unrelated personal ventures (e.g., the Veterans Corner nonprofit), which maintain independent repositories and governance per ADR-011's case-by-case philosophy. If you're asked to add content for something outside the buzonas.dev portfolio domain, flag that before proceeding — it likely belongs in a different repository.

## 3. Document Map

| Document | Purpose | Edit frequency |
|----------|---------|-----------------|
| [`portfolio-PRD.md`](docs/portfolio/portfolio-PRD.md) | Product requirements — audiences, section specs, release phases, open questions | Low — changes when scope or priorities change |
| [`portfolio-TAD.md`](docs/portfolio/portfolio-TAD.md) | Technical architecture — stack, repo structure, routing, CI/CD, environment topology | Low — changes when architecture changes |
| [`portfolio-decision-backlog.md`](docs/portfolio/portfolio-decision-backlog.md) | Open questions and resolution history for the portfolio | Living — append as new questions surface |
| [`design-system-PRD.md`](docs/design-system/design-system-PRD.md) | Design system product requirements — vision, audiences, release phases, open architectural questions | Low — changes when scope or priorities change |
| [`design-system-decision-backlog.md`](docs/design-system/design-system-decision-backlog.md) | Open architectural questions for the design system; tracks background from retired drafts | Living — rows resolved into ADRs as decisions are made |
| [`ADRs.md`](docs/ADRs.md) | Index of architectural decisions | Append-only — grows over time |
| [`docs/adr/*.md`](docs/adr/) | Individual MADR-format decision records, one file per decision | Append-only — never edit an accepted ADR's substance after the fact (see §5) |
| [`buzonas-brand-guide.md`](buzonas-brand-guide.md) | Color system, typography, signature visual elements, voice & tone | Low — changes when brand identity changes |
| [`component-inventory.md`](component-inventory.md) | Page/component specification — props, data sources, MVP status | Medium — updated as components are built or scope shifts |
| `README.md` | Repo orientation for humans landing here first | Low |
| `LICENSE` | All-rights-reserved — governs this repo's content only | Rarely |

## 4. Editing Conventions

- **This repo is prose and Markdown only.** No code samples should be added here beyond illustrative snippets already present in portfolio-TAD.md/CLAUDE.md examples — those exist to specify intent for the implementation repo, not to be executable here.
- **Cross-references use relative links within this repo** (e.g., `docs/adr/001-....md`) and **absolute GitHub URLs when referencing the site repo** (per the existing pattern in ADR-011: "CLAUDE.md in `buzonas.dev` should reference governance documents by URL, not relative path").
- **Don't restate content across documents.** If a fact belongs in the brand guide, it shouldn't also be copied into the PRD. Reference instead of duplicate — duplication is how these documents drift out of sync.
- **Status fields are load-bearing.** PRD open questions, ADR statuses, and component-inventory MVP markers (🔴/🟡/🟢) are read by both humans and AI assistants to determine what's actually decided versus still open. Don't silently flip a status — change it deliberately and note why.
- **Document versioning is semantic**, for every document in this repository that carries a version header (portfolio-PRD.md, portfolio-TAD.md, buzonas-brand-guide.md, component-inventory.md, and any subproject PRD or TAD such as design-system-PRD.md). ADRs are exempt — they don't carry version numbers, they're immutable per §5.
    - **Major** stays `0` while a document is in draft/unaccepted status. It increments to `1` only when the document is explicitly accepted by the owner, and increments further only for changes significant enough to constitute a new baseline (owner's judgment, not automatic).
    - **Minor** increments for substantive, semantic content changes — a section rewritten, scope added or removed, a requirement changed.
    - **Patch** increments for typographical or other non-semantic edits — wording cleanup that doesn't change meaning, formatting fixes, broken links.
    - A document's own header states its current version; this file is the definition of what that number means, not a place to redefine it per-document.

## 5. ADR Conventions (This Domain)

- **Format:** MADR — header block (`Date`, `Status`, and optionally `Supersedes`) followed by body sections: Context, Decision, Rationale, Consequences, Alternatives Considered. See [ADR-013](docs/adr/ADR-013-madr-format-for-decision-records.md).
- **Location:** One file per decision in `docs/adr/`, named `ADR-NNN-short-title-slug.md`, zero-padded to three digits. The `ADR-` prefix is kept in the filename (not just the directory) so individual records remain self-identifying in file-tool searches, editor tabs, and anywhere the `docs/adr/` path context isn't visible.
- **Numbering:** Sequential within this governance domain (buzonas.dev portfolio), starting at 001. Before creating a new ADR, check the highest-numbered file in `docs/adr/` to determine the next number — do not rely on a hardcoded count in this file. Do not renumber existing ADRs.
- **Immutability:** An `Accepted` ADR's Context/Decision/Rationale is not edited after the fact to reflect new thinking — that's what a new ADR is for. If a later decision changes or reverses an earlier one, write a new ADR with a `**Supersedes:** ADR-NNN` line in the header block, and update the *old* ADR's **Status** field to `Superseded by [ADR-NNN](ADR-NNN-slug.md)`. Both directions link to each other. The historical record stays intact.
- **Index discipline:** Every new ADR file must be added to the table in `docs/ADRs.md` in the same change. An ADR that exists only in `docs/adr/` and not in the index is incomplete.
- **Decision authority — hard gate.** The repository owner is the sole decision-maker for every ADR's Decision. An AI assistant working in this repository may research and present alternatives, lay out tradeoffs, and draft an ADR's prose once a decision has actually been made — but does not choose the decision, does not write a Decision line unprompted, and does not present a completed ADR as a fait accompli for the owner to simply accept or reject after the fact. See §9 for how this fits the broader PRD → ADR → TAD sequence.

## 6. Relationship to the Site Repository

The site repository (`sbuzonas/buzonas.dev`) treats every document in this repository as binding unless its own `CLAUDE.md` or an explicit developer instruction in-session says otherwise. Concretely:

- The site's `CLAUDE.md` is a **condensed, implementation-focused derivative** of `portfolio-TAD.md` + `docs/ADRs.md` + `buzonas-brand-guide.md` here — not a separate source of truth. If the two ever conflict, the documents in *this* repo win, and the site's `CLAUDE.md` should be corrected to match.
- When an architectural or brand decision changes, **update it here first**, then propagate the relevant summary into the site's `CLAUDE.md`. This repo is documentation-first by design (PRD §1.2; ADR-013) — code changes follow decisions recorded here, not the other way around.
- The site's `README.md` and `LICENSE` reference this repository's governance docs by absolute GitHub URL. Don't rename or move files here without checking those references.

## 7. What Not To Do Here

- Don't add buzonas.dev application code, components, or config files to this repository.
- Don't fork brand-guide color tokens or typography rules into a second copy "for convenience" — the site's `tailwind.config.ts` is the only place those values are implemented; this repo is the spec, not a second implementation.
- Don't collapse multiple ADRs into one file for convenience, even temporarily — if a concatenated view is needed for a specific context window or knowledge-base upload, generate it as a throwaway artifact outside this repo, not as a committed file. (This repo previously had exactly this problem with a concatenated ADR index; see `docs/ADRs.md` and the individual-file structure in §5 for the corrected pattern.)
- Subproject governance documents (PRD, TAD, decision backlog) live in `docs/<scope>/` where `<scope>` is the project slug — e.g., `docs/portfolio/`, `docs/design-system/`. ADRs remain flat in `docs/adr/` and are shared across the ecosystem; they are not scoped per project. Don't create a `docs/<scope>/` directory speculatively — only when a concrete subproject has an accepted PRD. This governance topology is set by [ADR-021](docs/adr/ADR-021-unified-subproject-governance-topology.md).
- **Don't draft, propose, or number an ADR as a side effect of other work.** If a decision point surfaces while doing something else — writing a PRD, preparing a handoff, answering an unrelated question — flag it explicitly and stop there. Do not fold a Decision into whatever artifact is already in progress. This has happened before in this repository's history and is the specific failure mode §5 and §9 exist to prevent.
- Don't treat a drafted PRD section as accepted scope until the owner has said so. A PRD in progress is a proposal, not a source of truth to architect against yet.

## 8. Requirements → Decisions → Architecture Workflow

Three document types in this repository have a strict dependency order, not just a topical relationship:

**PRD → ADR → TAD.** Requirements must exist before decisions are made; decisions must exist before architecture is documented. Each stage is a prerequisite for the one after it, not a parallel or optional activity.

- **PRD** defines the problem, vision, audience, and requirements for a product or subproject. This is where scope is framed — no implementation decisions belong here, only what's needed and why.
- **ADR** records a decision made in service of an existing PRD requirement. Every ADR should be traceable back to a requirement it's satisfying. An ADR is not the place to invent a new requirement — if a decision point reveals a need the PRD doesn't cover yet, the correct move is to flag it for a PRD update (or the PRD owner to update), not to fold it into the ADR's Context as if it were already established scope.
- **TAD** reflects the architecture that follows from accepted ADRs — either the current, as-built state, or a specific near-future target actively being iterated toward. TAD content should be traceable to the ADR(s) that produced it; it is a record of accumulated decisions, not an independent design document.

**How ADRs feed back:** an ADR's outcome is one of two things, sometimes both — it resolves an implementation question (update the TAD to reflect it), or it surfaces a previously unstated requirement (update the PRD to capture it). Both are legitimate, expected outcomes. Neither happens automatically or silently: the ADR records the decision itself; propagating that decision into the PRD or TAD is a deliberate, separate edit, not something folded into the ADR file.

**For AI assistants:** this sequence is the mechanism behind the §5 and §7 hard gates, not a separate rule. Don't draft ADR content until the relevant PRD requirement is settled and agreed by the owner. Don't draft TAD content until the relevant ADR is actually decided (by the owner, not proposed by the assistant). If asked to help with a document out of this order — an ADR before its PRD requirement exists, a TAD update before the ADR that would justify it — say so and ask how to proceed, rather than filling the gap with an assumed decision.

---

*CLAUDE.md v1.2 — buzonas.dev-governance Repository*
*This file governs how AI assistants work in this repository specifically. For the site's implementation contract, see buzonas.dev's own CLAUDE.md.*
