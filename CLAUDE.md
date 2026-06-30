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
| [`PRD.md`](PRD.md) | Product requirements — audiences, section specs, release phases, open questions | Low — changes when scope or priorities change |
| [`TAD.md`](TAD.md) | Technical architecture — stack, repo structure, routing, CI/CD, environment topology | Low — changes when architecture changes |
| [`ADRs.md`](ADRs.md) | Index of architectural decisions | Append-only — grows over time |
| [`docs/adr/*.md`](docs/adr/) | Individual MADR-format decision records, one file per decision | Append-only — never edit an accepted ADR's substance after the fact (see §5) |
| [`buzonas-brand-guide.md`](buzonas-brand-guide.md) | Color system, typography, signature visual elements, voice & tone | Low — changes when brand identity changes |
| [`component-inventory.md`](component-inventory.md) | Page/component specification — props, data sources, MVP status | Medium — updated as components are built or scope shifts |
| `README.md` | Repo orientation for humans landing here first | Low |
| `LICENSE` | All-rights-reserved — governs this repo's content only | Rarely |

## 4. Editing Conventions

- **This repo is prose and Markdown only.** No code samples should be added here beyond illustrative snippets already present in TAD.md/CLAUDE.md examples — those exist to specify intent for the implementation repo, not to be executable here.
- **Cross-references use relative links within this repo** (e.g., `docs/adr/001-....md`) and **absolute GitHub URLs when referencing the site repo** (per the existing pattern in ADR-011: "CLAUDE.md in `buzonas.dev` should reference governance documents by URL, not relative path").
- **Don't restate content across documents.** If a fact belongs in the brand guide, it shouldn't also be copied into the PRD. Reference instead of duplicate — duplication is how these documents drift out of sync.
- **Status fields are load-bearing.** PRD open questions, ADR statuses, and component-inventory MVP markers (🔴/🟡/🟢) are read by both humans and AI assistants to determine what's actually decided versus still open. Don't silently flip a status — change it deliberately and note why.

## 5. ADR Conventions (This Domain)

- **Format:** MADR — Context, Decision, Rationale, Consequences, Alternatives Considered. See [ADR-013](docs/adr/013-madr-format-for-decision-records.md).
- **Location:** One file per decision in `docs/adr/`, named `ADR-NNN-short-title-slug.md`, zero-padded to three digits. The `ADR-` prefix is kept in the filename (not just the directory) so individual records remain self-identifying in file-tool searches, editor tabs, and anywhere the `docs/adr/` path context isn't visible.
- **Numbering:** Sequential within this governance domain (buzonas.dev portfolio), starting at 001. Before creating a new ADR, check the highest-numbered file in `docs/adr/` to determine the next number — do not rely on a hardcoded count in this file. Do not renumber existing ADRs.
- **Immutability:** An `Accepted` ADR's Context/Decision/Rationale is not edited after the fact to reflect new thinking — that's what a new ADR is for. If a later decision changes or reverses an earlier one, write a new ADR, and update the *old* ADR's **Status** field to `Superseded` with a link to the replacement. The historical record stays intact.
- **Index discipline:** Every new ADR file must be added to the table in `ADRs.md` in the same change. An ADR that exists only in `docs/adr/` and not in the index is incomplete.

## 6. Relationship to the Site Repository

The site repository (`sbuzonas/buzonas.dev`) treats every document in this repository as binding unless its own `CLAUDE.md` or an explicit developer instruction in-session says otherwise. Concretely:

- The site's `CLAUDE.md` is a **condensed, implementation-focused derivative** of `TAD.md` + `ADRs.md` + `buzonas-brand-guide.md` here — not a separate source of truth. If the two ever conflict, the documents in *this* repo win, and the site's `CLAUDE.md` should be corrected to match.
- When an architectural or brand decision changes, **update it here first**, then propagate the relevant summary into the site's `CLAUDE.md`. This repo is documentation-first by design (PRD §1.2; ADR-013) — code changes follow decisions recorded here, not the other way around.
- The site's `README.md` and `LICENSE` reference this repository's governance docs by absolute GitHub URL. Don't rename or move files here without checking those references.

## 7. What Not To Do Here

- Don't add buzonas.dev application code, components, or config files to this repository.
- Don't fork brand-guide color tokens or typography rules into a second copy "for convenience" — the site's `tailwind.config.ts` is the only place those values are implemented; this repo is the spec, not a second implementation.
- Don't collapse multiple ADRs into one file for convenience, even temporarily — if a concatenated view is needed for a specific context window or knowledge-base upload, generate it as a throwaway artifact outside this repo, not as a committed file. (This repo previously had exactly this problem with a concatenated `ADRs.md`; see the index/individual-file structure in §5 for the corrected pattern.)
- Don't write a subproject's full governance set into `subprojects/` speculatively — that scaffolding is deferred until a concrete subproject exists (per current developer instruction; revisit if that changes).

---

*CLAUDE.md v1.0 — buzonas.dev-governance Repository*
*This file governs how AI assistants work in this repository specifically. For the site's implementation contract, see buzonas.dev's own CLAUDE.md.*
