# ADR-003: shadcn/ui as Component Library

**Date:** 2026-06-29
**Status:** Accepted

## Context

The site requires accessible, styled UI primitives — dialogs, buttons, cards, badges, navigation components. Options range from fully custom to fully managed component libraries.

## Decision

Use **shadcn/ui** as the component foundation.

## Rationale

- Components are copied into the repository (`/components/ui/`) — owned code, not a black-box dependency
- Built on Radix UI primitives — WCAG 2.1 AA accessible by default
- Unstyled base with Tailwind CSS variants — fully customizable to match brand guide without fighting the library
- `components.json` configuration file controls component generation
- No runtime dependency — components are TypeScript source files in the repo
- Widely adopted, well-documented, actively maintained

## Consequences

- **Positive:** Full ownership of component code, accessible defaults, Tailwind-native, no version lock-in
- **Negative:** Components must be manually updated when shadcn/ui releases improvements (no package version bump)
- **Constraint:** `/components/ui/` directory is generated — do not hand-edit generated files; customize via Tailwind variants or wrapper components

## Alternatives Considered

- **Radix UI directly** — more setup required; shadcn/ui is Radix UI with sensible defaults already applied
- **Chakra UI** — runtime CSS-in-JS; conflicts with Tailwind approach; bundle size concerns
- **Headless UI** — Tailwind-native but narrower component set than Radix/shadcn
- **Custom from scratch** — excessive for this scope; accessibility implementation is non-trivial

---
