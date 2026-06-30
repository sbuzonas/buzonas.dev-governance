# ADR-007: TypeScript over JavaScript

**Date:** 2026-06-29
**Status:** Accepted

## Context

The project will be built with AI coding assistance (Claude) as a primary development tool. The codebase will also serve as a portfolio artifact demonstrating engineering practice.

## Decision

Use **TypeScript** with strict mode enabled throughout the project.

## Rationale

- Type safety is essential for AI-assisted development — TypeScript errors surface incorrect AI-generated code at compile time rather than runtime
- Strict mode (`"strict": true` in `tsconfig.json`) eliminates entire categories of bugs
- Data files in `/data/` are TypeScript — content types (Project, BlogPost, Experience) are checked at build time; malformed data fails the build rather than silently rendering incorrectly
- The portfolio demonstrates engineering discipline — TypeScript strict mode is the correct signal
- Next.js 15 is TypeScript-native; the ecosystem assumes TypeScript

## Consequences

- **Positive:** Compile-time safety, better AI code generation quality, portfolio signal, IDE support
- **Negative:** Slightly more verbose than JavaScript; type annotations required for all data structures
- **Configuration:** `tsconfig.json` with `"strict": true`, `"noUncheckedIndexedAccess": true`

## Alternatives Considered

- **JavaScript** — rejected; loses type safety benefits critical for AI-assisted development and portfolio signal

---
