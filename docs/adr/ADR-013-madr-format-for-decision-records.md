# ADR-013: MADR Format for Decision Records

**Date:** 2026-06-29
**Status:** Accepted

## Context

Architectural decisions require a consistent, parseable format for both human review and AI coding assistant consumption. Several ADR formats exist: Michael Nygard's original format, Y-Statements, RFC-style, and MADR (Markdown Architectural Decision Records).

## Decision

Use **MADR (Markdown Architectural Decision Records)** format for all decision records in this project.

## Rationale

- MADR is Markdown-native — renders correctly in GitHub, is parseable by AI coding assistants, and requires no special tooling
- Structured sections (Context, Decision, Rationale, Consequences, Alternatives Considered) enforce complete thinking without being prescriptive about length
- Lightweight — no tooling dependency; ADRs are plain `.md` files
- MADR is the most widely adopted format in the current ecosystem; examples and tooling are available
- The format is well-suited for AI context injection — a coding assistant can parse an ADR and understand not just what was decided but why, and what was rejected

## Consequences

- **Positive:** No tooling dependency, GitHub-renderable, AI-parseable, complete reasoning captured
- **Negative:** Manual — no automated ADR numbering or status tracking; managed via the index table in this document
- **Process:** New ADRs are appended to this file and added to the index. Status changes are updated in place with a note.

## Alternatives Considered

- **Nygard format** (original) — minimal structure; lacks Alternatives Considered section; rejected
- **RFC format** — heavyweight; appropriate for team/org-scale decisions; over-engineered for this project
- **Y-Statements** — single-sentence format; insufficient context for AI consumption; rejected

---

*ADR Document v1.0 — buzonas.dev Portfolio Site*
*Next document: Component & Page Inventory*
