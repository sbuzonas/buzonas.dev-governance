# ADR-021: Governance Topology — Unified Subproject Governance in the Companion Repo

**Date:** 2026-07-01
**Status:** Accepted

## Context

ADR-011 established two repositories — the portfolio site and a `buzonas.dev-governance` companion —
under a case-by-case repository philosophy, and noted that future projects would have their own
repositories. ADR-016 then vendored the governance repository into the site as a submodule so its
documents are readable during coding sessions.

Since then the family has grown. The design system was added as an in-scope subproject with its own
code repository (ADR-017) and its own product and architecture documentation. That growth surfaced a
question ADR-011 did not settle: when a subproject gets its own code repository, does its
*governance* — requirements, technical architecture, decision records — also move to a separate
repository, or stay in the shared companion? The documentation had begun following a `docs/<scope>/`
layout with ADRs shared flat in `docs/adr/`, but on precedent rather than an explicit decision.

This decision concerns *in-scope* subprojects of the buzonas.dev portfolio domain. Unrelated ventures
— for example, the Veterans Corner nonprofit — remain outside this repository's scope and keep
independent governance per ADR-011's case-by-case philosophy.

## Decision

- Governance for in-scope subprojects of the buzonas.dev family — its product requirements (PRD),
  technical architecture (TAD), decision backlog, and architectural decision records — is **unified
  in the single `buzonas.dev-governance` companion repository**, not fragmented into per-subproject
  governance repositories.
- A subproject's own governance documents — its **PRD, TAD, and decision backlog** — live under
  **`docs/<scope>/`**, where `<scope>` is the project slug (for example, `docs/portfolio/`,
  `docs/design-system/`).
- **ADRs are shared flat in `docs/adr/`, numbered sequentially across the whole ecosystem** rather
  than per subproject, because architectural decisions frequently cross-cut subprojects and a single
  sequence keeps their identity unambiguous.
- This is **independent of code-repository topology.** A subproject may own a separate code repository
  (as the design system does, per ADR-017) while its governance remains here. Code repositories fork
  per project as toolchain, cadence, or ownership warrant (ADR-011); governance does not fork with
  them.
- The design system is the first subproject with its own code repository to keep its governance under
  this pattern: its PRD and decision backlog under `docs/design-system/` — with its TAD to follow
  there as its architecture is recorded — and its decisions in the shared `docs/adr/` sequence. (The
  portfolio's governance already lives this way under `docs/portfolio/`, PRD and TAD together.)

## Rationale

- Governance documents share a single purpose and audience — the authoritative reference for humans
  and AI assistants across the family — and have no build dependency on any subproject's code. This
  extends ADR-011's original reasoning; keeping the documents together avoids fragmenting that one
  reference across repositories.
- Keeping a subproject's PRD, TAD, and decision backlog together under one `docs/<scope>/` path keeps
  the PRD → ADR → TAD chain a locally coherent set, while the shared ADR sequence ties cross-cutting
  decisions across the family.
- A single ecosystem-wide ADR sequence prevents number collisions and lets a cross-cutting decision
  be referenced unambiguously from any subproject.
- Separating governance per subproject would multiply cross-repository coordination for decisions that
  already cross boundaries, with no offsetting benefit — the inverse of what repository scoping is
  for (ADR-011: consolidate when separation would only add coordination).
- Decoupling governance placement from code placement resolves the apparent contradiction of a
  subproject owning a separate code repository yet not a separate governance repository: the two are
  scoped on different criteria.

## Consequences

- **Positive:** One authoritative governance surface for the family; unambiguous cross-cutting ADR
  numbering; the `docs/<scope>/` convention now rests on an explicit decision rather than precedent.
- **Positive:** Clarifies retroactively why the design system, with its separate code repository,
  keeps its governance here, and coheres with ADR-016's submodule model — the site vendored the one
  shared governance repository, not a set of per-subproject ones.
- **Boundary:** This applies to in-scope subprojects of the buzonas.dev domain only; out-of-scope
  ventures keep independent governance (ADR-011). Whether a new effort is in scope remains a
  case-by-case judgment.
- **Constraint:** A `docs/<scope>/` directory is created only when a concrete subproject has accepted
  governance, not speculatively; a subproject's TAD follows its accepted PRD and decisions rather than
  preceding them; and ADRs are never scoped into per-project subdirectories.

## Alternatives Considered

- **A separate governance repository per subproject** — rejected: it fragments the family's single
  authoritative reference and forces cross-repository coordination for the many decisions that
  cross-cut subprojects, gaining nothing, since governance documents have no build or toolchain tie to
  a specific subproject's code.
- **Per-subproject ADR numbering** (each subproject its own sequence) — rejected: cross-cutting
  decisions would have ambiguous identity and cross-references between subprojects would be fragile; a
  single shared sequence is simpler and matches how the decisions actually cross boundaries.
- **Continuing the `docs/<scope>/` structure on precedent, without an ADR** — rejected: the structure
  is load-bearing — both humans and AI assistants rely on it — yet had no explicit decision behind it.
  Leaving a convention that governance depends on undocumented is the gap this record closes.
