# Component & Page Inventory
## buzonas.dev — Personal Portfolio & Professional Brand Site

**Version:** 1.1
**Owner:** Steve Buzonas
**Status:** Accepted
**Last Updated:** 2026-07-02
**Companion Documents:** docs/portfolio/portfolio-PRD.md, docs/portfolio/portfolio-TAD.md, docs/ADRs.md, buzonas-brand-guide.md

---

## Overview

This inventory defines every page route and React component required for the MVP launch. Components are organized by type: pages (App Router routes), layout components, section components, shared/primitive components, and MDX components. Each entry includes its props interface, data source, and notes for AI-assisted implementation.

**Component ownership (per ADR-027).** This site is a consumer of the buzonas.dev design system. The theme-agnostic UI primitives it builds on come from the design-system core, and the buzonas brand-signature visual elements (§7.5–7.7 — the Appalachian silhouette, hex lattice, hex grid) are **buzonas brand assets** sourced from the brand's style package, not site-owned. The entries below describe the site's composition and its interim scaffold; where a component is a design-system or brand asset it is marked as such. The upstream-vs-site-local classification of the low-complexity shared primitives (§7.1 TechTag, §7.2 SectionEyebrow, §7.3 StatusBadge) is still open — they remain site-local for now.

### Status Legend

| Status | Meaning |
|--------|---------|
| 🔴 Required — MVP | Must be built before v1.0.0 tag |
| 🟡 Stub — MVP | Route or component exists but content is placeholder |
| 🟢 Phase 2 | Not required for MVP |

---

## 1. Pages (App Router Routes)

### 1.1 Home Page — `/`

**File:** `app/page.tsx`
**Status:** 🔴 Required — MVP
**Type:** Static page (no `generateStaticParams`)

The home page is a single long-scroll page composed of named anchor sections. All sections are rendered server-side. No client-side data fetching.

**Sections rendered (in order):**
1. `<Hero />` — `#hero`
2. `<Experience />` — `#experience`
3. `<Competencies />` — `#competencies`
4. `<Veteran />` — `#veteran`
5. `<Land />` — `#land`
6. `<Contact />` — `#contact`

**Data imports:**
```typescript
import { experience } from '@/data/experience'
import { competencies, certifications } from '@/data/competencies'
import { vso, military } from '@/data/vso'
```

---

### 1.2 Projects Index — `/projects`

**File:** `app/projects/page.tsx`
**Status:** 🔴 Required — MVP

Renders all project cards grouped by category. No pagination at MVP — full list rendered statically.

**Data source:** `data/projects.ts`
**Components used:** `<ProjectGrid />`, `<ProjectCard />`

---

### 1.3 Individual Project — `/projects/[slug]`

**File:** `app/projects/[slug]/page.tsx`
**Status:** 🔴 Required — MVP
**Type:** Dynamic — requires `generateStaticParams`

Renders a single project page. Template varies by `depth` field on the project:

| `depth` value | Template rendered |
|---------------|------------------|
| `card` | Summary card expanded — description, tags, links only |
| `case-study` | Problem → Approach → Outcome layout |
| `deep-dive` | Full MDX content + case study header |

**Data source:** `data/projects.ts` + optional `content/projects/[slug].mdx`
**Components used:** `<ProjectPage />`, conditionally `<MdxContent />`

```typescript
export async function generateStaticParams() {
  return projects.map((p) => ({ slug: p.slug }))
}
export const dynamicParams = false
```

---

### 1.4 Consulting Overview — `/consulting`

**File:** `app/consulting/page.tsx`
**Status:** 🔴 Required — MVP

Renders FancyGuy Technologies practice overview and service offering cards. Reference architecture section is a placeholder at MVP.

**Data source:** `data/consulting.ts`
**Components used:** `<ServiceCard />`, `<ArchitectureCard />` (placeholder state)

---

### 1.5 Reference Architecture — `/consulting/architectures/[slug]`

**File:** `app/consulting/architectures/[slug]/page.tsx`
**Status:** 🟡 Stub — MVP

Route exists to prevent 404s. Renders a "Coming Soon" state. Full implementation in Phase 2.

```typescript
export async function generateStaticParams() {
  return [] // No architectures at MVP — stub only
}
export const dynamicParams = false
```

---

### 1.6 Blog Index — `/blog`

**File:** `app/blog/page.tsx`
**Status:** 🔴 Required — MVP

Renders blog post listing. At MVP, zero posts exist — empty state component renders instead of post grid.

**Data source:** `lib/blog.ts` (reads `/content/blog/`)
**Components used:** `<PostGrid />` or `<EmptyBlog />`

---

### 1.7 Blog Post — `/blog/[slug]`

**File:** `app/blog/[slug]/page.tsx`
**Status:** 🔴 Required — MVP
**Type:** Dynamic — requires `generateStaticParams`

At MVP, no posts exist — `generateStaticParams` returns empty array. Route is fully implemented so first posts can be published without code changes.

**Data source:** `content/blog/[slug].mdx`
**Components used:** `<MdxContent />`, `<PostHeader />`, `<AuthorBlock />`

```typescript
export async function generateStaticParams() {
  const posts = await getBlogPosts()
  return posts.map((p) => ({ slug: p.slug }))
  // Returns [] at MVP — no posts yet
}
export const dynamicParams = false
```

---

### 1.8 404 Page

**File:** `app/not-found.tsx`
**Status:** 🔴 Required — MVP

Custom 404 matching site design. Includes navigation back to home. GitHub Pages serves this as `404.html`.

---

## 2. Layout Components

### 2.1 Root Layout

**File:** `app/layout.tsx`
**Status:** 🔴 Required — MVP

Wraps all pages. Responsibilities:
- Font loading (`Plus_Jakarta_Sans`, `JetBrains_Mono`) via `next/font/google`
- Theme FOUC prevention script (inline `<script>` in `<head>`)
- CSS variable injection (`globals.css`)
- `<Nav />` and `<Footer />` rendered around `{children}`
- Root metadata defaults (`title`, `description`, OpenGraph, JSON-LD person schema)
- Print stylesheet import (`styles/print.css`)

---

### 2.2 Navigation

**File:** `components/layout/Nav.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface NavProps {
  // No props — data is internal constants
}
```

**Behavior:**
- Transparent on hero, solid (`--color-deep`) background on scroll (IntersectionObserver or scroll listener)
- Logo / name left-aligned: "Steve Buzonas" in display font
- Nav links right-aligned: Experience · Projects · Consulting · Blog · Contact
- `<ThemeToggle />` rightmost
- Mobile: hamburger menu → full-screen nav overlay
- Active section highlighted via IntersectionObserver on anchor sections

**Note:** Nav uses `'use client'` for scroll behavior and mobile toggle. Keep client boundary narrow — extract scroll logic to a hook.

---

### 2.3 Footer

**File:** `components/layout/Footer.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface FooterProps {
  // No props — static content
}
```

**Content:**
- Copyright line: `© {year} Steve Buzonas · FancyGuy Technologies LLC`
- Social links: LinkedIn, GitHub, email
- Subtle brand tagline or none

---

### 2.4 Theme Toggle

**File:** `components/layout/ThemeToggle.tsx`
**Status:** 🔴 Required — MVP
**Directive:** `'use client'`

```typescript
interface ThemeToggleProps {
  // No props
}
```

**Behavior:**
- Reads current theme from `<html>` class
- Toggles between `dark` and `light` class on `<html>`
- Persists to `localStorage` (`theme` key)
- Icon: sun (light mode) / moon (dark mode) — Lucide React icons

---

## 3. Home Page Section Components

### 3.1 Hero

**File:** `components/sections/Hero.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface HeroProps {
  // No props — static content
}
```

**Structure:**
```
<section id="hero">
  <HexLattice />              // SVG — upper left
  <HexGrid />                 // SVG — upper right accent
  <div>                       // Content center
    [headshot image]
    <h1>Steve Buzonas</h1>
    <p>Senior Engineering Leader // Cloud Architect</p>
    <div>                     // CTA buttons
      [View My Work → #experience]
      [Download Resume → /resume/steve-buzonas-resume.pdf]
    </div>
  </div>
  <AppalachianSilhouette />   // SVG — bottom anchor
</section>
```

**Notes:**
- Full viewport height (`min-h-screen`)
- Background: `--gradient-hero` CSS gradient
- Entrance animation sequence per brand guide — CSS keyframes, `prefers-reduced-motion` wrapped
- Headshot: `<Image>` with `unoptimized` (static export constraint); rounded or masked per design
- SVG components are placeholders at MVP if assets are not ready (see ADR-010)

---

### 3.2 Experience

**File:** `components/sections/Experience.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface ExperienceProps {
  items: ExperienceEntry[]
}

interface ExperienceEntry {
  company: string
  role: string
  dateRange: string          // e.g. "2023–2026"
  location: string
  type: 'employment' | 'consulting' | 'military'
  bullets: string[]          // 2–4 impact statements
  current?: boolean
}
```

**Data source:** `data/experience.ts`

**Notes:**
- Vertical timeline layout — left border line, entries stacked
- Current role: `--color-sky` left border; past roles: `--color-iron`
- Military entry visually distinct — `--color-patriot` accent, positioned as foundation of timeline
- FancyGuy Technologies consulting listed as a single entry with "select engagements" note
- Section eyebrow: `// EXPERIENCE` in mono font

---

### 3.3 Competencies

**File:** `components/sections/Competencies.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface CompetenciesProps {
  domains: CompetencyDomain[]
  certifications: Certification[]
}

interface CompetencyDomain {
  name: string
  skills: Skill[]
}

interface Skill {
  name: string
  level: 'expert' | 'proficient' | 'familiar'
}

interface Certification {
  name: string
  issuer: string
  validUntil: string         // e.g. "2029"
  badgeUrl?: string
}
```

**Data source:** `data/competencies.ts`

**Notes:**
- Domain groups rendered as labeled sections (Cloud, Kubernetes & Platform, DevOps/SRE, etc.)
- Skill level visually differentiated — expert: full color; proficient: medium; familiar: muted
- Certifications prominently displayed at top of section with validity dates
- Current certs: AWS Solutions Architect Professional, AWS DevOps Engineer Professional, AWS Security Specialty (all 2026–2029)

---

### 3.4 Veteran

**File:** `components/sections/Veteran.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface VeteranProps {
  military: MilitaryService
  vsoAffiliations: VSOAffiliation[]
  nonprofit: NonprofitEntry
}

interface MilitaryService {
  branch: string             // "United States Marine Corps"
  unit: string               // "MWSS 471 Det A"
  rank: string               // "Corporal"
  dateRange: string          // "2004–2012"
  bullets: string[]
}

interface VSOAffiliation {
  name: string               // "VFW", "American Legion", etc.
  role?: string              // Optional current role
  url?: string
}

interface NonprofitEntry {
  name: string               // "Veterans Corner"
  status: 'forming'
  description: string
}
```

**Data source:** `data/vso.ts`

**Notes:**
- Section accent: `--color-patriot` (red) — visually distinct from tech sections
- Card border-top in patriot red per brand guide
- VSO affiliations as a grid of affiliation cards
- Veterans Corner framed accurately as "in formation" — no overstatement
- Military service tone: precise and factual, not sentimental

---

### 3.5 Land

**File:** `components/sections/Land.tsx`
**Status:** 🔴 Required — MVP (illustrated placeholder)

```typescript
interface LandProps {
  // No props — static content at MVP
}
```

**Notes:**
- Illustrated placeholder at MVP — CSS gradient or SVG landscape approximation in `--color-pine` tones
- Personal narrative copy — warm tone, the one section where warmth is fully appropriate
- Real photography deferred to Phase 2
- Section accent: `--color-pine`

---

### 3.6 Contact

**File:** `components/sections/Contact.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface ContactProps {
  // No props — static content
}
```

**Content:**
- Location: Pulaski, PA
- Email link (`mailto:sbuzonas@icloud.com`)
- LinkedIn: `linkedin.com/in/sbuzonas`
- GitHub: `github.com/sbuzonas`
- Resume download button → `/resume/steve-buzonas-resume.pdf`
- No contact form at MVP

---

## 4. Project Components

### 4.1 Project Grid

**File:** `components/projects/ProjectGrid.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface ProjectGridProps {
  projects: Project[]
  category?: ProjectCategory   // Optional filter — Phase 2
}
```

**Layout:** Responsive grid — 1 col mobile, 2 col tablet, 3 col desktop
**Notes:** Category filter UI deferred to Phase 2; all projects rendered at MVP

---

### 4.2 Project Card

**File:** `components/projects/ProjectCard.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface ProjectCardProps {
  project: Project
}

// Project type defined in data/projects.ts
interface Project {
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
  hasMdx: boolean
}
```

**Visual:**
- Dark surface card (`--color-deep`)
- Top border accent in `--color-sky` (2px)
- `<StatusBadge />` top-right
- Title, org, year
- Excerpt (2–3 sentences)
- Tech tag chips (mono font, `<TechTag />`)
- GitHub icon link (if `github` present)
- "View Project →" link to `/projects/[slug]`
- Hover: subtle sky glow via `box-shadow`

---

### 4.3 Project Page

**File:** `components/projects/ProjectPage.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface ProjectPageProps {
  project: Project
  mdxContent?: React.ComponentType   // Present when depth === 'deep-dive'
}
```

**Renders different layouts based on `project.depth`:**
- `card` → expanded card — header, full description, tags, links
- `case-study` → structured sections: Context, Approach, Outcome, Tech Stack
- `deep-dive` → case study header + full `<MdxContent />` below

---

## 5. Consulting Components

### 5.1 Service Card

**File:** `components/consulting/ServiceCard.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface ServiceCardProps {
  service: ServiceOffering
}

interface ServiceOffering {
  title: string              // e.g. "Cloud Architecture & Modernization"
  description: string
  capabilities: string[]     // Bullet list of specific capabilities
  icon?: string              // Lucide icon name — optional
}
```

**Data source:** `data/consulting.ts`

---

### 5.2 Architecture Card

**File:** `components/consulting/ArchitectureCard.tsx`
**Status:** 🟡 Stub — MVP

```typescript
interface ArchitectureCardProps {
  architecture: ReferenceArchitecture
}

interface ReferenceArchitecture {
  slug: string
  title: string
  description: string
  status: 'coming-soon'     // All stubs at MVP
  tags: string[]
}
```

**MVP state:** Renders "Coming Soon" card with title and description. Links to `/consulting/architectures/[slug]` stub page.

---

## 6. Blog Components

### 6.1 Post Grid

**File:** `components/blog/PostGrid.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface PostGridProps {
  posts: BlogPost[]
}
```

**Notes:** At MVP, `posts` is always empty array — component delegates to `<EmptyBlog />` when `posts.length === 0`

---

### 6.2 Post Card

**File:** `components/blog/PostCard.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface PostCardProps {
  post: BlogPost
}

interface BlogPost {
  slug: string
  title: string
  date: string
  category: 'tech' | 'veteran' | 'nature'
  excerpt: string
  tags: string[]
  readingTime: number        // Minutes
  published: boolean
}
```

**Notes:**
- Category drives accent color: tech = sky, veteran = patriot-red, nature = pine
- Reading time displayed in meta line
- Links to `/blog/[slug]`

---

### 6.3 Empty Blog

**File:** `components/blog/EmptyBlog.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface EmptyBlogProps {
  // No props
}
```

**Content:** Friendly empty state — "Posts coming soon" with category previews or simple message. Must not look broken.

---

### 6.4 Post Header

**File:** `components/blog/PostHeader.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface PostHeaderProps {
  title: string
  date: string
  category: 'tech' | 'veteran' | 'nature'
  tags: string[]
  readingTime: number
}
```

---

### 6.5 Author Block

**File:** `components/blog/AuthorBlock.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface AuthorBlockProps {
  // No props — static Steve Buzonas content
}
```

**Content:** Headshot, name, one-line bio, LinkedIn link. Appears at bottom of each blog post.

---

## 7. Shared / Primitive Components

### 7.1 Tech Tag

**File:** `components/shared/TechTag.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface TechTagProps {
  label: string
  size?: 'sm' | 'md'        // Default: 'sm'
}
```

**Visual:** Mono font, `--color-iron` background, `--color-sky` text, `--radius-sm` border radius

---

### 7.2 Section Eyebrow

**File:** `components/shared/SectionEyebrow.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface SectionEyebrowProps {
  label: string              // e.g. "EXPERIENCE" — rendered uppercase by CSS
  className?: string
}
```

**Visual:** JetBrains Mono, uppercase, `--color-mist`, wide letter-spacing (`0.15em`)

---

### 7.3 Status Badge

**File:** `components/shared/StatusBadge.tsx`
**Status:** 🔴 Required — MVP

```typescript
type ProjectStatus = 'complete' | 'active' | 'in-progress' | 'coming-soon'

interface StatusBadgeProps {
  status: ProjectStatus
}
```

**Visual:** Pill shape, color per status:
- `complete` → muted/iron
- `active` → sky (accent)
- `in-progress` → amber/warning
- `coming-soon` → muted/iron, italic

---

### 7.4 MDX Content

**File:** `components/shared/MdxContent.tsx`
**Status:** 🔴 Required — MVP

```typescript
interface MdxContentProps {
  content: React.ComponentType   // MDX component loaded via dynamic import
  className?: string
}
```

**Notes:**
- Thin wrapper around the dynamically imported MDX component
- Applies `prose` Tailwind typography classes for MDX rendering
- Custom component overrides defined in `mdx-components.tsx` at project root (Next.js convention)

---

### 7.5 Appalachian Silhouette

**File:** `components/shared/AppalachianSilhouette.tsx`
**Status:** 🟡 Stub — MVP
**Ownership:** buzonas brand asset — sourced from the brand style package (ADR-027); the states below describe the site-side interim placeholder.

```typescript
interface AppalachianSilhouetteProps {
  className?: string
}
```

**MVP state:** CSS gradient placeholder — dark navy ridge shape via `clip-path` or `border-radius` approximation. Replaced with inline SVG when asset workflow is resolved (ADR-010).
**Production:** Inline SVG of Pennsylvania conifer treeline. Colors via `currentColor` or CSS custom properties — theme-reactive.

---

### 7.6 Hex Lattice

**File:** `components/shared/HexLattice.tsx`
**Status:** 🟡 Stub — MVP
**Ownership:** buzonas brand asset — sourced from the brand style package (ADR-027); the states below describe the site-side interim placeholder.

```typescript
interface HexLatticeProps {
  className?: string
  opacity?: number           // Default: 0.1
}
```

**MVP state:** CSS placeholder or omitted entirely. Replaced with inline SVG (ADR-010).
**Production:** Inline SVG hexagonal lattice. Outline-only, `--color-sky` at specified opacity.

---

### 7.7 Hex Grid

**File:** `components/shared/HexGrid.tsx`
**Status:** 🟡 Stub — MVP
**Ownership:** buzonas brand asset — sourced from the brand style package (ADR-027); the states below describe the site-side interim placeholder.

```typescript
interface HexGridProps {
  className?: string
  opacity?: number           // Default: 0.08
}
```

**MVP state:** Same as HexLattice — placeholder or omitted. Right-corner accent element.

---

## 8. MDX Components (`mdx-components.tsx`)

**File:** `mdx-components.tsx` (project root — Next.js convention)
**Status:** 🔴 Required — MVP

Defines custom component overrides for standard HTML elements rendered from MDX. Applied globally to all MDX content on the site.

```typescript
import type { MDXComponents } from 'mdx/types'

export function useMDXComponents(components: MDXComponents): MDXComponents {
  return {
    h1: ({ children }) => <h1 className="...">{children}</h1>,
    h2: ({ children }) => <h2 className="...">{children}</h2>,
    h3: ({ children }) => <h3 className="...">{children}</h3>,
    p:  ({ children }) => <p className="...">{children}</p>,
    a:  ({ href, children }) => <a href={href} className="...">{children}</a>,
    code: ({ children }) => <code className="font-mono ...">{children}</code>,
    pre: ({ children }) => <pre className="...">{children}</pre>,
    blockquote: ({ children }) => <blockquote className="...">{children}</blockquote>,
    ul: ({ children }) => <ul className="...">{children}</ul>,
    ol: ({ children }) => <ol className="...">{children}</ol>,
    ...components,
  }
}
```

---

## 9. Data Files (`/data/`)

### 9.1 `data/experience.ts`

Source of truth for career timeline. Typed as `ExperienceEntry[]`. Populated from resume — all roles from 2001 to present including military service.

### 9.2 `data/projects.ts`

Source of truth for all project metadata. Typed as `Project[]`. MVP seed data: 6 resume highlights (Octothorpe, Course Map, Passport, Fetch-a-File, Vagrant OpsWorks, Academy of Voice) + FancyGuy consulting engagements.

### 9.3 `data/competencies.ts`

Source of truth for skills and certifications. Typed as `CompetencyDomain[]` and `Certification[]`. Current certs: 3 AWS (2026–2029).

### 9.4 `data/consulting.ts`

Source of truth for service offerings and reference architecture stubs. Typed as `ServiceOffering[]` and `ReferenceArchitecture[]`.

### 9.5 `data/vso.ts`

Source of truth for military service, VSO affiliations, and Veterans Corner nonprofit entry. Typed as `MilitaryService`, `VSOAffiliation[]`, `NonprofitEntry`.

---

## 10. Library Files (`/lib/`)

### 10.1 `lib/blog.ts`

```typescript
// Key exports
export async function getBlogPosts(): Promise<BlogPost[]>
export async function getBlogPost(slug: string): Promise<BlogPost | null>
```

Reads `/content/blog/` directory, parses frontmatter via `gray-matter`, filters `published: true`, sorts by date descending.

### 10.2 `lib/projects.ts`

```typescript
// Key exports
export function getProjects(category?: ProjectCategory): Project[]
export function getProject(slug: string): Project | undefined
export function getProjectsWithMdx(): Project[]
```

Reads from `data/projects.ts`. `getProjectsWithMdx()` returns only projects where `hasMdx: true` — used to determine whether to attempt MDX dynamic import.

---

## 11. MVP Component Summary

| Component | File | Status | Client? |
|-----------|------|--------|---------|
| Root Layout | `app/layout.tsx` | 🔴 | No |
| Home Page | `app/page.tsx` | 🔴 | No |
| Projects Index | `app/projects/page.tsx` | 🔴 | No |
| Project Detail | `app/projects/[slug]/page.tsx` | 🔴 | No |
| Consulting | `app/consulting/page.tsx` | 🔴 | No |
| Architecture Stub | `app/consulting/architectures/[slug]/page.tsx` | 🟡 | No |
| Blog Index | `app/blog/page.tsx` | 🔴 | No |
| Blog Post | `app/blog/[slug]/page.tsx` | 🔴 | No |
| 404 | `app/not-found.tsx` | 🔴 | No |
| Nav | `components/layout/Nav.tsx` | 🔴 | **Yes** |
| Footer | `components/layout/Footer.tsx` | 🔴 | No |
| ThemeToggle | `components/layout/ThemeToggle.tsx` | 🔴 | **Yes** |
| Hero | `components/sections/Hero.tsx` | 🔴 | No |
| Experience | `components/sections/Experience.tsx` | 🔴 | No |
| Competencies | `components/sections/Competencies.tsx` | 🔴 | No |
| Veteran | `components/sections/Veteran.tsx` | 🔴 | No |
| Land | `components/sections/Land.tsx` | 🔴 | No |
| Contact | `components/sections/Contact.tsx` | 🔴 | No |
| ProjectGrid | `components/projects/ProjectGrid.tsx` | 🔴 | No |
| ProjectCard | `components/projects/ProjectCard.tsx` | 🔴 | No |
| ProjectPage | `components/projects/ProjectPage.tsx` | 🔴 | No |
| ServiceCard | `components/consulting/ServiceCard.tsx` | 🔴 | No |
| ArchitectureCard | `components/consulting/ArchitectureCard.tsx` | 🟡 | No |
| PostGrid | `components/blog/PostGrid.tsx` | 🔴 | No |
| PostCard | `components/blog/PostCard.tsx` | 🔴 | No |
| EmptyBlog | `components/blog/EmptyBlog.tsx` | 🔴 | No |
| PostHeader | `components/blog/PostHeader.tsx` | 🔴 | No |
| AuthorBlock | `components/blog/AuthorBlock.tsx` | 🔴 | No |
| TechTag | `components/shared/TechTag.tsx` | 🔴 | No |
| SectionEyebrow | `components/shared/SectionEyebrow.tsx` | 🔴 | No |
| StatusBadge | `components/shared/StatusBadge.tsx` | 🔴 | No |
| MdxContent | `components/shared/MdxContent.tsx` | 🔴 | No |
| AppalachianSilhouette | `components/shared/AppalachianSilhouette.tsx` | 🟡 | No |
| HexLattice | `components/shared/HexLattice.tsx` | 🟡 | No |
| HexGrid | `components/shared/HexGrid.tsx` | 🟡 | No |
| MDX Components | `mdx-components.tsx` | 🔴 | No |

**Total MVP components:** 37
**Client components (`'use client'`):** 2 — Nav, ThemeToggle

---

*Component & Page Inventory v1.1 — buzonas.dev Portfolio Site*
*Next document: CLAUDE.md — AI Coding Context*
