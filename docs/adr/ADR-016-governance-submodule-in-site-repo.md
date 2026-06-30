# ADR-016: Governance Repo Included as Git Submodule in Site Repo

**Date:** 2026-06-30
**Status:** Accepted
**Supersedes:** ADR-011 (consequences — "CLAUDE.md in `buzonas.dev` should reference governance documents by URL, not relative path")

## Context

ADR-011 established a two-repository structure and included a consequence that CLAUDE.md in
`buzonas.dev` should reference governance documents by URL rather than relative path. This
constraint was premised on the expectation that AI coding assistants could retrieve external URLs
on demand during a coding session.

In practice, Claude Code — the AI coding assistant used for this project — does not support
arbitrary web fetches during coding sessions. It accesses files via local file paths using a
`Read` tool. URLs in CLAUDE.md §14 are therefore inert: the assistant cannot follow them to
retrieve the referenced documents, rendering the cross-references broken for their intended
purpose.

The governance documents (PRD, TAD, ADRs, brand guide, component inventory) are the authoritative
source for architectural and brand decisions. If the AI coding assistant cannot access them during
a session, it operates on a summarized copy in CLAUDE.md alone — which is explicitly documented
as a condensed derivative, not a source of truth (governance CLAUDE.md §6).

## Decision

Include `sbuzonas/buzonas.dev-governance` as a Git submodule in `sbuzonas/buzonas.dev` at
`.governance/`.

CLAUDE.md §14 in the site repo will reference governance documents via `.governance/`-prefixed
relative paths instead of bare repository slugs or GitHub URLs.

## Rationale

- The submodule makes governance documents available as local files during coding sessions,
  enabling Claude Code to `Read` them directly
- The two-repo structure from ADR-011 is preserved — the governance repo retains its identity
  as a standalone repository; the submodule is a versioned reference, not a merge
- The governance repo continues to function as a standalone AI context source when accessed
  independently
- Submodule pinning ensures the site repo and governance docs are always at compatible versions;
  a governance update does not silently affect active coding sessions
- The constraint being superseded was a workaround for a tool limitation, not a structural
  preference — removing it when the tool model changed is correct

## Consequences

- **Positive:** Governance documents are accessible to Claude Code during site coding sessions;
  CLAUDE.md references are no longer inert
- **Positive:** Submodule commit pinning ties governance state to site state — both sides of a
  cross-repo decision change together in the same PR sequence
- **Changed:** CLAUDE.md §14 references use `.governance/` relative paths instead of URL slugs
- **Changed:** `git clone` for the site repo requires `--recurse-submodules`; CI/CD workflows
  require `submodules: true` on `actions/checkout` or an explicit `git submodule update --init`
  step
- **Unchanged:** The governance→site reference direction continues to use absolute GitHub URLs
  (governance CLAUDE.md §4 convention is not affected)
- **Unchanged:** Cross-references within the governance repo continue to use relative links
- **Negative:** Contributors who clone without `--recurse-submodules` will have an empty
  `.governance/` directory; this is mitigated by updating README clone instructions

## Alternatives Considered

- **Continue using URL references in CLAUDE.md** — rejected; URLs are inert for Claude Code's
  `Read` tool, so the references serve no functional purpose and only create an illusion of
  accessibility
- **Add governance docs to Claude Code session manually each time** — rejected; this is the
  current workaround and requires per-session setup; the submodule solves it durably
- **Copy governance docs directly into the site repo** — rejected; this creates a second
  authoritative copy and the divergence/duplication problem that ADR-011 was designed to avoid
- **Merge the repos into a monorepo** — rejected; ADR-011's rationale for separation remains
  valid (independent versioning, governance as a standalone portfolio signal, no build dependency)

---

*ADR-016 — buzonas.dev Portfolio Site*
