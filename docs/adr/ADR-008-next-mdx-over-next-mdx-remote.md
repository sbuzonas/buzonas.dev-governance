# ADR-008: @next/mdx over next-mdx-remote

**Date:** 2026-06-29
**Status:** Accepted

## Context

MDX content in `/content/blog/` and `/content/projects/` must be rendered in dynamic Next.js App Router pages (`/blog/[slug]` and `/projects/[slug]`). Two primary libraries exist for MDX rendering in Next.js: `@next/mdx` (first-party, Vercel-maintained) and `next-mdx-remote` (third-party, Hashicorp-maintained).

## Decision

Use **@next/mdx** for MDX rendering.

## Rationale

- First-party Vercel/Next.js package — maintained alongside Next.js itself; breaking changes are coordinated
- Fewer dependencies: `@next/mdx`, `@mdx-js/loader`, `@mdx-js/react` vs. `next-mdx-remote` + serialization layer
- Native React Server Component support — MDX files render as RSC by default; no `/rsc` subpath workaround required
- Supports `import` and `export` statements within MDX files — enables project deep-dives to embed interactive components
- Dynamic import pattern (`await import('@/content/blog/${slug}.mdx')`) with `generateStaticParams` satisfies the file-backed content architecture cleanly
- `mdx-components.tsx` at project root provides global component overrides — standard Next.js pattern, well-documented
- `next-mdx-remote` RSC support was historically marked unstable; active maintenance has shifted toward community fork (`next-mdx-remote-client`)

## Content Architecture Pattern

```typescript
// app/blog/[slug]/page.tsx
export async function generateStaticParams() {
  const posts = await getBlogPosts() // reads /content/blog/ via gray-matter
  return posts.map((post) => ({ slug: post.slug }))
}

export default async function BlogPost({ params }: { params: { slug: string } }) {
  const { default: Post, metadata } = await import(`@/content/blog/${params.slug}.mdx`)
  return <Post />
}

export const dynamicParams = false
```

## Consequences

- **Positive:** First-party support, fewer dependencies, native RSC, import/export in MDX, cleaner architecture
- **Negative:** MDX files must be compiled by Next.js build pipeline — content is tightly coupled to the build; cannot load MDX from external sources at runtime (not a requirement for this project)
- **Constraint:** `gray-matter` still required alongside `@next/mdx` — used to enumerate slugs from frontmatter for `generateStaticParams` before dynamic import

## Alternatives Considered

- **next-mdx-remote** — rejected; extra dependency, RSC stability concerns, no import/export in MDX, active maintenance shifting to community fork
- **next-mdx-remote-client** — community fork with better RSC support; rejected in favor of first-party solution with fewer total dependencies

---
