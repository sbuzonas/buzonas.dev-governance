# ADR-004: MDX for Long-Form Content

**Date:** 2026-06-29
**Status:** Accepted

## Context

The site includes long-form content in multiple sections: blog posts, project write-ups, and eventually reference architecture documentation. A content authoring and storage strategy is required.

## Decision

Use **MDX files** stored in `/content/` as the content layer for all long-form text.

## Rationale

- MDX extends Markdown with JSX component support — blog posts can embed interactive components, diagrams, and code blocks without breaking the authoring flow
- File-based content is version-controlled alongside source code — no CMS dependency, no external service, no API calls at runtime
- Frontmatter (YAML) provides structured metadata (title, date, category, tags, status) parsed at build time
- Content in `/content/` is separate from application code in `/app/` and `/components/` — clean separation of concerns
- MDX files are portable — content is not locked to any platform or rendering library

## Consequences

- **Positive:** Version-controlled content, no external dependencies, JSX in content, portable
- **Negative:** No visual editor — all content authored in raw MDX; acceptable for a technical author
- **Constraint:** All MDX processed at build time; no runtime content updates without a rebuild and redeploy

## Alternatives Considered

- **Contentlayer** — elegant type-safe content layer; currently unmaintained (archived 2024); rejected
- **CMS (Sanity, Contentful)** — adds external dependency, cost, and complexity inconsistent with the site's scope and philosophy
- **Plain Markdown** — lacks JSX component support; rejected in favor of MDX for flexibility

---
