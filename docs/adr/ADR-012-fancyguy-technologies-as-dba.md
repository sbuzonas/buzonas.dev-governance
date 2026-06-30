# ADR-012: FancyGuy Technologies as DBA under buzonas.dev Personal Brand

**Date:** 2026-06-29
**Status:** Accepted

## Context

Steve Buzonas operates FancyGuy Technologies LLC as an independent consulting practice. The portfolio site at `buzonas.dev` serves both job-seeking (personal brand) and consulting (business development) purposes. A decision is required on how the two brand identities relate on the site.

## Decision

**FancyGuy Technologies operates as a DBA / practice name under the `buzonas.dev` personal brand.** The consulting section at `/consulting` presents FancyGuy Technologies as the operating entity while `buzonas.dev` remains the primary brand surface.

Framing: *"FancyGuy Technologies — Steve Buzonas, Principal Consultant"*

## Rationale

- FancyGuy Technologies is a formal LLC — the legal entity name is fixed and carries existing brand equity
- During active job seeking, a unified personal brand surface reduces cognitive load for hiring managers — two distinct brands at the same URL creates ambiguity about intent
- The DBA framing preserves FancyGuy identity without requiring a separate domain, separate brand guide, or separate site at this stage
- If a full-time role is accepted, FancyGuy Technologies can continue operating independently; the consulting section remains accurate without changes
- If FancyGuy Technologies grows to warrant its own domain (`fancyguy.tech` or similar), `/consulting` content migrates with minimal rework — the content model is self-contained

## Consequences

- **Positive:** Single brand surface for job seeking, FancyGuy equity preserved, future separation is clean
- **Negative:** Some ambiguity between "hire Steve" (employment) and "hire FancyGuy" (consulting) — managed through section placement and tone, not brand separation
- **Constraint:** FancyGuy Technologies name used consistently throughout the consulting section — never "my company" or unnamed

## Future Migration Trigger

If FancyGuy Technologies wins engagements that warrant a dedicated marketing presence, spin out to `fancyguy.tech` with its own site. The `/consulting` section on `buzonas.dev` becomes a pointer to the standalone site.

---
