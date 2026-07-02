# Technical Architecture Document
## buzonas.dev — Personal Portfolio & Professional Brand Site

**Version:** 1.0
**Owner:** Steve Buzonas
**Status:** Accepted
**Last Updated:** 2026-07-01
**Companion Documents:** docs/portfolio/portfolio-PRD.md, buzonas-brand-guide.md

---

## 1. Architecture Overview

buzonas.dev is a statically exported Next.js application deployed to GitHub Pages. There is no server runtime, no database, and no backend service. All content is file-based — MDX for blog posts and project write-ups, TypeScript data files for structured content (career timeline, competencies, project metadata). The CI/CD pipeline is GitHub Actions. The architecture is deliberately simple: the complexity lives in the content and design, not the infrastructure.

```
┌─────────────────────────────────────────────────────┐
│                   GitHub (Source)                    │
│  ┌──────────────┐         ┌────────────────────────┐ │
│  │  main branch  │────────▶│   GitHub Actions CI    │ │
│  └──────────────┘  push   └────────────┬───────────┘ │
│                                        │              │
│  ┌──────────────┐  tag    ┌────────────▼───────────┐ │
│  │  vX.Y.Z tag  │────────▶│   GitHub Actions CD    │ │
│  └──────────────┘         └────────────┬───────────┘ │
└───────────────────────────────────────-┼─────────────┘
                                         │
              ┌──────────────────────────┼──────────────────────────┐
              │                          │                           │
              ▼                          ▼                           │
   ┌─────────────────────┐   ┌─────────────────────┐               │
   │  GitHub Pages       │   │  GitHub Pages        │               │
   │  staging.buzonas.dev│   │  buzonas.dev         │               │
   │  (every main push)  │   │  (version tag only)  │               │
   └─────────────────────┘   └─────────────────────┘               │
```

### Key Constraints Driving Architecture

- **Static export required** — GitHub Pages serves only static files; no Node.js runtime
- **No API routes** — Next.js API routes are incompatible with `output: 'export'`
- **No ISR / SSR** — all pages must be statically generated at build time
- **No server components with dynamic data** — all data fetching at build time via `generateStaticParams` and file-system reads

---

## 2. Repository Structure

### 2.1 Repository Philosophy

Repositories are scoped per artifact when toolchains, dependencies, and pipelines are meaningfully distinct. Tightly coupled artifacts that share a common toolchain are co-located. This decision is made case-by-case and documented in the meta governance repository.

### 2.2 Repository Inventory

| Repository | Purpose | Visibility |
|-----------|---------|------------|
| `sbuzonas/buzonas.dev` | Main portfolio site — Next.js application + MDX content | Public |
| `sbuzonas/buzonas.dev-governance` | Meta project — ADRs, PRD, brand guide, project planning | Public |
| `sbuzonas/veterans-corner` | Veterans Corner nonprofit — documents, planning, outreach | TBD |

> **Note:** The governance repository is itself a portfolio signal — it demonstrates engineering discipline and documentation practice. Keep it well-maintained.

### 2.3 Main Site Repository Layout

```
buzonas.dev/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml              # Lint, typecheck, build — runs on every push to main
│   │   └── cd.yml              # Deploy — staging on push, production on tag
│   ├── PULL_REQUEST_TEMPLATE.md
│   └── ISSUE_TEMPLATE/
│       ├── bug.md
│       └── content.md
├── app/                        # Next.js App Router
│   ├── layout.tsx              # Root layout — fonts, theme provider, nav, footer
│   ├── page.tsx                # Home page — all anchor sections
│   ├── projects/
│   │   ├── page.tsx            # /projects index
│   │   └── [slug]/
│   │       └── page.tsx        # /projects/[slug] individual project page
│   ├── consulting/
│   │   ├── page.tsx            # /consulting overview
│   │   └── architectures/
│   │       └── [slug]/
│   │           └── page.tsx    # /consulting/architectures/[slug] — Phase 2
│   ├── blog/
│   │   ├── page.tsx            # /blog index
│   │   └── [slug]/
│   │       └── page.tsx        # /blog/[slug] MDX post
│   ├── globals.css             # Tailwind base + CSS custom properties
│   └── not-found.tsx           # 404 page
├── components/
│   ├── ui/                     # shadcn/ui generated components (do not hand-edit)
│   ├── layout/
│   │   ├── Nav.tsx
│   │   ├── Footer.tsx
│   │   └── ThemeToggle.tsx
│   ├── sections/               # Home page section components
│   │   ├── Hero.tsx
│   │   ├── Experience.tsx
│   │   ├── Competencies.tsx
│   │   ├── Veteran.tsx
│   │   ├── Land.tsx
│   │   └── Contact.tsx
│   ├── projects/
│   │   ├── ProjectCard.tsx
│   │   ├── ProjectGrid.tsx
│   │   └── ProjectPage.tsx
│   ├── consulting/
│   │   ├── ServiceCard.tsx
│   │   └── ArchitectureCard.tsx
│   ├── blog/
│   │   ├── PostCard.tsx
│   │   ├── PostGrid.tsx
│   │   └── EmptyBlog.tsx
│   └── shared/
│       ├── TechTag.tsx
│       ├── SectionEyebrow.tsx
│       ├── AppalachianSilhouette.tsx   # SVG component — brand signature element
│       ├── HexLattice.tsx              # SVG component — geometric network
│       └── StatusBadge.tsx
├── content/
│   ├── blog/                   # MDX blog posts
│   │   └── .gitkeep
│   ├── projects/               # MDX project write-ups (optional per project)
│   │   └── .gitkeep
│   └── architectures/          # MDX reference architecture docs (Phase 2)
│       └── .gitkeep
├── data/                       # Structured content — TypeScript data files
│   ├── experience.ts           # Career timeline entries
│   ├── projects.ts             # Project metadata (card data)
│   ├── competencies.ts         # Skills, tools, certifications
│   ├── consulting.ts           # Service offerings
│   └── vso.ts                  # VSO affiliations + veteran section content
├── lib/
│   ├── mdx.ts                  # MDX file reading + frontmatter parsing
│   ├── projects.ts             # Project data access helpers
│   └── blog.ts                 # Blog post data access helpers
├── public/
│   ├── resume/
│   │   └── steve-buzonas-resume.pdf
│   ├── images/
│   │   ├── headshot.jpg        # Profile image (edited from LinkedIn)
│   │   └── og-default.png      # Default OpenGraph image
│   ├── CNAME                   # GitHub Pages custom domain
│   └── robots.txt
├── styles/
│   └── print.css               # Print stylesheets (color + B&W)
├── next.config.ts
├── tailwind.config.ts
├── tsconfig.json
├── components.json             # shadcn/ui configuration
├── .env.example
└── CLAUDE.md                   # AI coding context (see separate document)
```

---

## 3. Technology Stack

### 3.1 Core Framework

| Layer | Technology | Version | Rationale |
|-------|-----------|---------|-----------|
| Framework | Next.js | 15.x | App Router, static export support, native MDX integration |
| Language | TypeScript | 5.x | Type safety; required for AI-assisted development at this complexity |
| Styling | Tailwind CSS | 4.x | Utility-first; pairs cleanly with shadcn/ui; config-driven brand tokens |
| UI Components | shadcn/ui | Latest | Accessible, unstyled-base components; owned in-repo, not a black-box dependency |
| MDX | @next/mdx | Latest | First-party Next.js MDX integration; native App Router + RSC support; fewer dependencies than next-mdx-remote |

### 3.2 Content & Data

| Concern | Approach | Rationale |
|---------|---------|-----------|
| Blog posts | MDX files in `/content/blog/` | Portable, version-controlled, no CMS dependency |
| Project write-ups | MDX files in `/content/projects/` | Same toolchain as blog; optional per project |
| Structured data | TypeScript files in `/data/` | Type-safe, co-located with code, no external data source |
| Frontmatter parsing | gray-matter | Standard, well-maintained; used alongside @next/mdx for slug listing |
| MDX rendering | @next/mdx | First-party; content loaded via `dynamic import('@/content/[slug].mdx')` with `generateStaticParams` |

### 3.3 Build & Deploy

| Concern | Technology | Notes |
|---------|-----------|-------|
| Static export | `output: 'export'` in next.config.ts | Required for GitHub Pages |
| Image optimization | `unoptimized: true` | Required for static export; images pre-optimized manually or via build script |
| CI/CD | GitHub Actions | Two workflows: ci.yml (push) + cd.yml (push + tag) |
| Hosting — Staging | GitHub Pages (gh-pages branch) | Deploys from main on every push |
| Hosting — Production | GitHub Pages (separate CNAME) | Deploys on semver tag |
| DNS | buzonas.dev CNAME → GitHub Pages | Already configured |

### 3.4 Quality & Accessibility

| Tool | Purpose |
|------|---------|
| ESLint + eslint-config-next | Linting |
| TypeScript strict mode | Type safety |
| Lighthouse CI | Performance, accessibility, SEO gates |
| axe-core / @axe-core/react | Accessibility scanning in CI |
| prettier | Code formatting |

### 3.5 Fonts

Loaded via `next/font/google` — zero layout shift, self-hosted at build time:
- `Plus_Jakarta_Sans` — display / headings
- `JetBrains_Mono` — mono / code / labels

---

## 4. Next.js Configuration

### 4.1 `next.config.ts`

```typescript
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  output: 'export',           // Static export for GitHub Pages
  trailingSlash: true,        // Required for GitHub Pages routing
  images: {
    unoptimized: true,        // Required for static export
  },
  // basePath: '',            // Empty — buzonas.dev is root domain, not a subpath
}

export default nextConfig
```

### 4.2 Static Export Constraints

The following Next.js features are **unavailable** under `output: 'export'` and must not be used:

- API Routes (`/app/api/`)
- Server Actions (form submissions, mutations)
- Incremental Static Regeneration (ISR / `revalidate`)
- Middleware
- `next/image` with remote optimization (use `unoptimized: true`)
- Dynamic routes without `generateStaticParams`

**All dynamic routes must export `generateStaticParams`** to enumerate slugs at build time. Missing this causes a build failure — not a runtime 404.

---

## 5. Routing Architecture

### 5.1 Route Map

| Route | Type | Data Source | Notes |
|-------|------|-------------|-------|
| `/` | Static | `data/*.ts` | All home sections as anchor links |
| `/projects` | Static | `data/projects.ts` | Project card index |
| `/projects/[slug]` | Dynamic | `data/projects.ts` + `content/projects/[slug].mdx` | `generateStaticParams` required |
| `/consulting` | Static | `data/consulting.ts` | Practice overview + service offerings |
| `/consulting/architectures/[slug]` | Dynamic | `content/architectures/[slug].mdx` | Phase 2 — stub at launch |
| `/blog` | Static | `lib/blog.ts` reads `/content/blog/` | Empty state at launch |
| `/blog/[slug]` | Dynamic | `content/blog/[slug].mdx` | `generateStaticParams` required |

### 5.2 Home Page Anchor Sections

The home page (`/`) is a single-page layout with anchor navigation:

```
/                     → full page
/#experience          → Career Timeline
/#competencies        → Skills & Certifications
/#veteran             → Veteran & Nonprofit (Veterans Corner)
/#land                → Land & Conservation
/#contact             → Contact / CTA
```

Nav links to `/projects` and `/consulting` are top-level routes, not anchors.

### 5.3 404 Handling

GitHub Pages serves a `404.html` for unmatched routes. Next.js static export generates `404.html` from `app/not-found.tsx`. Custom 404 page should match site design and provide navigation back to home.

---

## 6. Content Architecture

### 6.1 MDX Content Model

**Blog Post Frontmatter:**
```yaml
---
title: string
date: YYYY-MM-DD
category: tech | veteran | nature
excerpt: string        # Used in post cards and OpenGraph description
tags: string[]
readingTime: number    # Minutes — computed or manual
published: boolean     # false = draft, excluded from build
---
```

**Project MDX Frontmatter:**
```yaml
---
title: string
slug: string
category: professional | consulting | opensource | personal
status: complete | active | in-progress | coming-soon
year: number | string  # e.g. 2018 or "2021-2023"
org: string            # Employer, client, or personal
tags: string[]
github: string | null
demo: string | null
excerpt: string
depth: card | case-study | deep-dive   # Controls page template rendered
published: boolean
---
```

### 6.2 Structured Data Files

TypeScript data files in `/data/` are the source of truth for all structured content. They are type-checked at build time and imported directly by page components — no API calls, no fetch, no async.

```typescript
// data/projects.ts — example shape
export interface Project {
  slug: string
  title: string
  category: 'professional' | 'consulting' | 'opensource' | 'personal'
  status: 'complete' | 'active' | 'in-progress' | 'coming-soon'
  year: string
  org: string
  tags: string[]
  github?: string
  demo?: string
  excerpt: string
  depth: 'card' | 'case-study' | 'deep-dive'
  hasMdx: boolean     // true = /content/projects/[slug].mdx exists
}
```

### 6.3 MDX Rendering Strategy

- `@next/mdx` is configured in `next.config.ts` via `withMDX` wrapper
- MDX files in `/content/` are loaded via dynamic import in page components: `await import('@/content/blog/${slug}.mdx')`
- `generateStaticParams` enumerates slugs at build time by reading the filesystem with `gray-matter`
- `mdx-components.tsx` in project root defines custom component overrides — maps HTML elements to styled equivalents
- Code blocks: syntax highlighted via `rehype-pretty-code` + `shiki` configured as rehype plugin in `next.config.ts`
- All MDX processed at build time — zero client-side MDX runtime shipped

---

## 7. Theming Architecture

### 7.1 Theme Strategy

Three rendering contexts require explicit styling:

| Context | Implementation |
|---------|---------------|
| Dark mode (primary) | Default; CSS custom properties on `:root` |
| Light mode | CSS custom properties on `[data-theme="light"]` or `.light` class |
| Print — color | `@media print` + `[data-print="color"]` |
| Print — B&W | `@media print` + `[data-print="bw"]` |

### 7.2 Theme Provider

- System preference detected via `prefers-color-scheme` media query on initial load
- Manual toggle stored in `localStorage` (`theme` key)
- Theme class applied to `<html>` element to avoid flash of unstyled content (FOUC)
- Toggle component in Nav — icon switches between sun/moon

**FOUC Prevention:** Inline script in `<head>` (before React hydration) reads `localStorage` and applies theme class synchronously. This is the standard Next.js pattern and is safe for static export.

```html
<!-- In app/layout.tsx <head> — runs before paint -->
<script dangerouslySetInnerHTML={{ __html: `
  (function() {
    const theme = localStorage.getItem('theme') ||
      (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
    document.documentElement.classList.add(theme);
  })();
` }} />
```

### 7.3 CSS Custom Properties

Brand tokens defined in `app/globals.css` as CSS custom properties, mapped to Tailwind via `tailwind.config.ts`:

```css
:root {
  --color-void:    #0A0F1E;
  --color-deep:    #0D1B2A;
  --color-steel:   #1B3A5C;
  --color-sky:     #A8D4E6;
  --color-glacier: #C9E8F5;
  --color-mist:    #7BA7BC;
  --color-patriot: #8B1A2B;
  --color-iron:    #2E3F50;
  --color-pine:    #1A2B1E;
  --color-cloud:   #EFF6FB;
}

.light {
  /* Light mode overrides — not inverted dark; deliberately specified */
  --color-bg-primary:   #F0F7FB;
  --color-bg-surface:   #FFFFFF;
  --color-text-primary: #0D1B2A;
  --color-text-muted:   #1B3A5C;
}
```

### 7.4 Print Stylesheets

`styles/print.css` is imported in `app/layout.tsx`. Print styles:

- Suppress: Nav, Footer, hero animations, decorative SVGs (hex lattice, silhouette), theme toggle, CTAs
- Preserve: Name, title, experience timeline, competencies, contact info
- Color print: maintain brand palette (deep navy headings, sky accents)
- B&W print: force black text, white background, no color accents (`print-color-adjust: exact` overridden)
- Page breaks: avoid splitting timeline entries or project cards across pages

---

## 8. CI/CD Pipeline Architecture

### 8.1 Workflow: `ci.yml` — Continuous Integration

**Trigger:** Push to `main`, Pull Request to `main`

```
Push to main
     │
     ▼
┌─────────────────────────────────────────┐
│              ci.yml                      │
│                                          │
│  1. Checkout + Setup Node.js 22.x        │
│  2. Install dependencies (npm ci)        │
│  3. TypeScript typecheck (tsc --noEmit)  │
│  4. ESLint                               │
│  5. next build (static export)           │
│  6. Lighthouse CI (score gates)          │
│  7. axe-core accessibility scan          │
│  8. Dead link check (lychee)             │
└──────────────────┬──────────────────────┘
                   │ success
                   ▼
        ┌──────────────────────┐
        │  cd.yml — staging    │
        │  deploy to           │
        │  staging.buzonas.dev │
        └──────────────────────┘
```

### 8.2 Workflow: `cd.yml` — Continuous Deployment

**Trigger — Production:** Push of `v*.*.*` tag OR published GitHub Release

> **Note:** GitHub Pages enforces one custom domain per repository. A `staging.buzonas.dev` subdomain is not achievable from the same repository without a separate repo — which violates the repo philosophy. Staging environment is deferred to a future hosting migration phase (Vercel or Cloudflare Pages). See ADR-009.

```
┌──────────────────────────────────────────────┐
│                  cd.yml                       │
│                                              │
│  if: tag matches v*.*.* OR release published │
│  ├── Build static export                     │
│  ├── Write CNAME: buzonas.dev                │
│  └── Deploy → gh-pages branch               │
└──────────────────────────────────────────────┘
```

### 8.3 GitHub Pages Branch Strategy

| Branch | Purpose | CNAME |
|--------|---------|-------|
| `main` | Source code | — |
| `gh-pages` | Production static output | `buzonas.dev` |

`gh-pages` is a **generated artifact** — never manually edited. The Actions workflow has write permission via `GITHUB_TOKEN`.

### 8.4 Pre-Release Validation (Replaces Staging)

Until a staging environment is available, validation occurs locally against the production build artifact before tagging a release:

```bash
npm run build        # Generates /out static export
npx serve out        # Serve locally for visual review
npx lhci autorun     # Lighthouse CI against local build
```

The release checklist (§6.5 of PRD) is enforced manually before tagging.

### 8.4 Lighthouse CI Gates

Configured via `lighthouserc.js` or `.lighthouserc.json`:

```json
{
  "ci": {
    "assert": {
      "assertions": {
        "categories:performance":    ["error", { "minScore": 0.9 }],
        "categories:accessibility":  ["error", { "minScore": 0.9 }],
        "categories:best-practices": ["error", { "minScore": 0.9 }],
        "categories:seo":            ["error", { "minScore": 0.9 }]
      }
    }
  }
}
```

Failed Lighthouse gates block the staging deploy.

---

## 9. SEO & Metadata Architecture

### 9.1 Metadata Strategy

All metadata defined via Next.js App Router `generateMetadata()` or static `metadata` exports — no third-party SEO library needed.

```typescript
// app/layout.tsx — site-wide defaults
export const metadata: Metadata = {
  metadataBase: new URL('https://buzonas.dev'),
  title: {
    default: 'Steve Buzonas — Senior Engineering Leader',
    template: '%s | Steve Buzonas',
  },
  description: 'Senior Engineering Leader & Cloud Architect...',
  openGraph: {
    type: 'website',
    locale: 'en_US',
    url: 'https://buzonas.dev',
    siteName: 'Steve Buzonas',
    images: [{ url: '/images/og-default.png', width: 1200, height: 630 }],
  },
  twitter: {
    card: 'summary_large_image',
    creator: '@sbuzonas',
  },
}
```

### 9.2 Structured Data (JSON-LD)

Person schema injected in root layout:

```typescript
const personSchema = {
  '@context': 'https://schema.org',
  '@type': 'Person',
  name: 'Steve Buzonas',
  url: 'https://buzonas.dev',
  jobTitle: 'Senior Engineering Leader',
  sameAs: [
    'https://linkedin.com/in/sbuzonas',
    'https://github.com/sbuzonas',
  ],
}
```

### 9.3 Sitemap & Robots

- `app/sitemap.ts` — Next.js native sitemap generation; enumerates all static and dynamic routes
- `public/robots.txt` — permissive; no disallow rules at launch

---

## 10. Environment Topology

| Environment | URL | Source | Deploy Trigger | Purpose |
|-------------|-----|--------|----------------|---------|
| Local dev | `localhost:3000` | Any branch | `npm run dev` | Development |
| Local build preview | `localhost:3000` (via `npx serve out`) | Any branch | `npm run build` | Pre-release validation |
| Production | `buzonas.dev` | `main` @ semver tag | `v*.*.*` tag or GitHub Release | Live site |

> **Staging environment** (e.g. `staging.buzonas.dev`) is deferred to a future hosting migration phase. GitHub Pages does not support multiple custom domains from a single repository. Pre-release validation uses a local production build. See ADR-009.

### Environment Variables

No secrets required at launch — the site is fully static with no API calls at runtime. Build-time variables if needed:

```bash
# .env.local (not committed)
NEXT_PUBLIC_SITE_URL=http://localhost:3000

# Set in GitHub Actions environment
NEXT_PUBLIC_SITE_URL=https://buzonas.dev   # production
NEXT_PUBLIC_SITE_URL=https://staging.buzonas.dev  # staging
```

---

## 11. Performance Architecture

### 11.1 Font Loading

```typescript
// app/layout.tsx
import { Plus_Jakarta_Sans, JetBrains_Mono } from 'next/font/google'

const jakarta = Plus_Jakarta_Sans({
  subsets: ['latin'],
  weight: ['300', '400', '600', '700', '800'],
  variable: '--font-display',
  display: 'swap',
})

const jetbrains = JetBrains_Mono({
  subsets: ['latin'],
  weight: ['400', '500'],
  variable: '--font-mono',
  display: 'swap',
})
```

Fonts are self-hosted at build time by `next/font` — zero runtime network requests, no FOUT.

### 11.2 Image Strategy

- Static export requires `unoptimized: true` — Next.js image optimization pipeline unavailable
- Images pre-optimized before commit: WebP format, appropriate dimensions, compressed
- Hero SVG elements (silhouette, hex lattice) are inline SVG components — zero image requests, fully scalable
- OpenGraph image: static PNG in `/public/images/`

### 11.3 Animation Performance

- All animations use CSS transforms and opacity — compositor-layer only, no layout thrashing
- Hero entrance sequence orchestrated via CSS animation delays, not JavaScript
- `prefers-reduced-motion` media query wraps all keyframe animations
- No animation libraries at launch (Framer Motion deferred to Phase 2 if needed)

---

## 12. Dependency Philosophy

Keep the dependency tree shallow. Every dependency is a maintenance liability. Before adding a package, ask: can this be done in ~20 lines of TypeScript?

**Approved at launch:**

| Package | Purpose | Alternatives Considered |
|---------|---------|------------------------|
| next | Framework | — |
| react / react-dom | UI runtime | — |
| tailwindcss | Styling | CSS Modules (more verbose) |
| @tailwindcss/typography | Prose styling for MDX content | Custom prose styles (more work) |
| shadcn/ui components | Accessible UI primitives | Radix UI directly (more setup) |
| @next/mdx | MDX rendering — first-party, native RSC | next-mdx-remote (extra dependency, historically unstable with RSC) |
| @mdx-js/loader + @mdx-js/react | Required peers for @next/mdx | — |
| gray-matter | Frontmatter parsing for slug listing | — |
| rehype-pretty-code | Code syntax highlighting | rehype-highlight (less featured) |
| shiki | Syntax highlighter (used by rehype-pretty-code) | prism (heavier) |

**Deferred to Phase 2:**
- Framer Motion (animation)
- Pagefind (static search)
- Plausible / Fathom (analytics)

---

## 13. Open Technical Decisions

| # | Decision | Resolution | Status |
|---|---------|------------|--------|
| T-1 | MDX library: `next-mdx-remote` vs `@next/mdx` | **`@next/mdx`** — first-party, fewer dependencies, native RSC, supports import/export in MDX. Captured in ADR-008. | Resolved |
| T-2 | Staging subdomain: `staging.buzonas.dev` from same repo | **Not viable** — GitHub Pages enforces one custom domain per repository. Staging deferred to future hosting migration. Captured in ADR-009. | Resolved |
| T-3 | `gh-pages-staging` branch vs separate repo for staging | **Moot** — resolved by T-2. Single `gh-pages` branch for production only. | Resolved |
| T-4 | SVG implementation: inline components vs. static files | **Hybrid** — inline SVG React components for themeable brand elements (silhouette, hex lattice); static files or icon library for simple icons. SVG asset workflow pending Canva Pro evaluation. Captured in ADR-010. | Partially resolved |

---

*TAD v1.0 — buzonas.dev Portfolio Site*
*Next document: ADRs*
