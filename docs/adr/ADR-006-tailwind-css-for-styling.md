# ADR-006: Tailwind CSS for Styling

**Date:** 2026-06-29
**Status:** Accepted

## Context

The site has a defined brand guide with a specific color system, typography scale, and spacing system. A styling approach must be chosen that supports brand token implementation, dark/light mode theming, print stylesheets, and component-level customization.

## Decision

Use **Tailwind CSS v4** as the primary styling system.

## Rationale

- Utility-first approach eliminates unused CSS at build time — optimal for performance
- Brand tokens defined as CSS custom properties in `globals.css` and extended in `tailwind.config.ts` — single source of truth
- Dark mode implemented via class strategy (`.dark` on `<html>`) — compatible with system preference detection and manual toggle
- shadcn/ui (ADR-003) is Tailwind-native — no friction between component library and styling approach
- `@tailwindcss/typography` plugin provides prose styling for MDX content without custom CSS
- Tailwind v4 introduces CSS-first configuration — further reduces JavaScript configuration overhead

## Consequences

- **Positive:** Performance, brand token integration, dark mode support, shadcn compatibility
- **Negative:** Utility class verbosity in JSX; mitigated by component extraction
- **Constraint:** Print stylesheets require explicit `@media print` blocks in `styles/print.css` — Tailwind's print variant (`print:`) is used for simple overrides; complex print layout requires dedicated CSS

## Alternatives Considered

- **CSS Modules** — more verbose, no built-in design system, no shadcn compatibility
- **Styled Components / Emotion** — CSS-in-JS; runtime overhead; conflicts with RSC model
- **Vanilla CSS** — viable but loses design token and utility benefits

---
