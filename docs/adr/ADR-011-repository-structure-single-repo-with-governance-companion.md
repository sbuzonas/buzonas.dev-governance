# ADR-011: Repository Structure — Single Site Repo with Governance Companion

**Date:** 2026-06-29
**Status:** Accepted

## Context

The project spans multiple concerns: the portfolio site application, long-form content, and project governance documentation (PRD, TAD, ADRs, brand guide). A repository structure decision is required.

## Decision

Maintain **two repositories**:

1. **`sbuzonas/buzonas.dev`** — Next.js application, `/content/` MDX files, `/data/` TypeScript data files, `/public/` assets
2. **`sbuzonas/buzonas.dev-governance`** — PRD, TAD, ADRs, brand guide, project planning documents

## Rationale

- Application code and content share a toolchain (Next.js build), pipeline (GitHub Actions), and deployment target — co-location is correct
- Governance documents (PRD, TAD, ADRs) have no build dependency on the application — they are reference artifacts consumed by humans and AI coding assistants
- Separating governance enables the `buzonas.dev-governance` repo to serve as a standalone AI context source — CLAUDE.md in the site repo can reference governance docs without them cluttering the application tree
- The governance repo is itself a portfolio signal: it demonstrates documentation discipline and architectural thinking independently of the code
- Veterans Corner nonprofit and other future projects will have their own repositories per the case-by-case philosophy

## Repository Philosophy (General)

Repositories are scoped when:
- Toolchains, dependencies, or pipelines are meaningfully distinct
- Independent versioning, access control, or deployment cadence is required
- Co-location would create coupling without benefit

Repositories are consolidated when:
- Artifacts share a build pipeline and deploy together
- Separation would require cross-repo coordination for routine changes

This philosophy is applied case-by-case and is not a rigid rule.

## Consequences

- **Positive:** Clean separation of concerns, governance repo as standalone AI context, portfolio signal
- **Negative:** Cross-repo references require absolute URLs; governance updates are a separate commit/PR
- **Constraint:** CLAUDE.md in `buzonas.dev` should reference governance documents by URL, not relative path

---
