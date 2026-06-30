# ADR-010: SVG Strategy — Hybrid Inline Components and Static Files

**Date:** 2026-06-29
**Status:** Accepted

## Context

The brand guide specifies signature SVG elements: the Appalachian tree silhouette and geometric hex lattice. These elements must respond to the light/dark theme toggle (colors change with theme). Additional icon-scale SVGs are required throughout the UI.

## Decision

Use a **hybrid SVG strategy**:
- **Inline SVG React components** for brand signature elements (silhouette, hex lattice, hex grid accent)
- **Static SVG files or icon library** for UI icons and simple decorative elements

## Rationale

**Inline SVG components** for brand elements:
- CSS custom properties (`var(--color-sky)`, `var(--color-void)`) apply directly to SVG `fill` and `stroke` attributes
- Theme switching (dark ↔ light) propagates to SVGs automatically via CSS — no JavaScript required
- SVGs are React components: they accept props, respond to responsive breakpoints, and can be animated via CSS
- No additional HTTP requests — SVGs render with the component tree

**Static files or icon library** for icons:
- Simple icons (social links, nav toggles, status indicators) do not require theme-reactive colors
- Standard icon libraries (Lucide React — already a shadcn/ui dependency) cover the common cases
- Reduces component complexity for non-brand elements

## SVG Asset Workflow

Brand SVGs (silhouette, hex lattice) do not currently exist as source files. Workflow to be evaluated:
- **Canva Pro AI** — evaluate export quality and SVG cleanliness for inline use
- **Manual SVG authoring** — Figma or direct path editing if Canva output requires cleanup
- **Placeholder approach at launch** — CSS gradient + border-radius approximation of silhouette if SVG assets are not ready; replaced in Phase 2

This workflow decision is deferred until SVG asset evaluation is complete. The component interface (`<AppalachianSilhouette />`, `<HexLattice />`) is defined regardless — implementation switches from placeholder to inline SVG without changing consuming components.

## Consequences

- **Positive:** Theme-reactive brand elements, no extra HTTP requests, React-composable
- **Negative:** Inline SVG increases component file size; large or complex SVGs may impact readability
- **Constraint:** Brand SVG source files are not yet available; placeholder implementation required at launch if asset workflow is not resolved before MVP

## Alternatives Considered

- **Static SVG files with CSS filter** — can approximate color theming via `filter: invert()` but produces imprecise results; rejected
- **All inline SVGs** — would include icon-scale SVGs inline; unnecessary complexity when Lucide React covers standard icons
- **Image files (PNG/WebP)** — no theming capability; rejected for brand elements

---
