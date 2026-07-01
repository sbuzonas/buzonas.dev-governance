# Product Requirements Document
## buzonas.dev — Personal Portfolio & Professional Brand Site

**Version:** 1.1
**Owner:** Steve Buzonas
**Status:** Accepted
**Last Updated:** 2026-07-01
**Tracking:** GitHub Issues + Projects

---

## 1. Purpose & Vision

### 1.1 Problem Statement

Steve Buzonas is an active job seeker with 20+ years of cloud architecture and engineering leadership experience, active veteran service commitments, and a multidimensional personal identity that a standard resume cannot convey. A resume communicates credentials; this site communicates the person behind them.

### 1.2 Vision

A personal portfolio site that serves simultaneously as:
- A **professional brand surface** for engineering leadership job seekers
- A **living demonstration** of modern frontend engineering practice (the site is itself a portfolio artifact)
- A **human-centered narrative** that reflects veteran service, Appalachian roots, and land stewardship alongside technical depth

### 1.3 Success Criteria

| Criterion | Measure |
|-----------|---------|
| Professional credibility | Hiring managers can assess experience, scope, and judgment within 60 seconds of landing |
| Differentiation | The site is visually and narratively distinct from generic developer portfolios |
| Technical signal | The stack, structure, and commit history itself demonstrates engineering maturity |
| Accessibility | WCAG 2.1 AA compliant at launch |
| Performance | Lighthouse score ≥ 90 across all categories |

---

## 2. Audiences & Goals

### 2.1 Primary Audience — Potential Employers & Recruiters

**Goal:** Establish credibility, communicate scope of experience, and create a memorable impression that extends the resume.

**Key questions they arrive with:**
- Has this person operated at enterprise scale?
- Can they lead teams and influence strategy, not just execute?
- What kind of person are they beyond the job titles?

**What the site must answer:**
- Yes — 200+ AWS accounts, 19 EKS clusters, HIPAA-regulated environments
- Yes — executive advising, architecture roadmaps, cross-functional leadership
- A Marine, a woodworker, a land steward, a veteran advocate — someone with roots

### 2.2 Secondary Audience — Engineering Peers & Collaborators

**Goal:** Establish peer credibility; invite collaboration and conversation.

**Key questions:**
- Do they go deep, or just manage?
- What have they actually built?
- Is there a blog worth following?

### 2.3 Tertiary Audience — Nonprofit / General Public / VSO Community

**Goal:** Communicate veteran service commitment and nonprofit formation to potential donors, partners, and fellow veterans.

### 2.4 Quaternary Audience — VSO Peers & Veteran Community

**Goal:** Connect with fellow veterans; establish shared identity and service context.

---

## 3. Site Sections & Content Requirements

### 3.1 Hero

**Purpose:** Immediate brand impression. Establish identity in under 5 seconds.

**Required elements:**
- Full name: Steve Buzonas
- Title line: Senior Engineering Leader // Cloud Architect
- Primary CTA: View My Work (anchors to experience/projects)
- Secondary CTA: Download Resume (PDF)
- Visual: Full-viewport hero with Appalachian silhouette, geometric network lattice, deep navy → sky blue gradient
- Navigation anchored to top on scroll

**Constraints:**
- Must render correctly on mobile without loss of silhouette element
- Hero image assets derived from approved brand guide

### 3.2 Career Timeline / Experience

**Purpose:** Communicate the arc and depth of a 20+ year career.

**Required elements:**
- Chronological timeline (reverse order)
- Each entry: company, title, date range, 2–3 impact statements (not job descriptions)
- Visual differentiation for current/most recent role
- Military service integrated into the timeline as a foundational entry, not an afterthought
- Consulting practice (FancyGuy Technologies) represented appropriately — select engagements surfaced

**Source:** Resume `sbuzonas-cv-plain-2026.pdf`

### 3.3 Projects (`/projects`)

**Purpose:** First-class showcase of what was actually built — not just managed. Spans professional, open source, personal/hobby, and consulting work. Serves both hiring managers (depth of craft) and potential consulting clients (range and capability).

**MVP Scope (Launch):**
- Project card index (`/projects`) — resume highlight cards with name, org/context, year, tech tags, brief description
- Every project card links to its own route (`/projects/[slug]`) — depth of content varies, but the route always exists
- Categories surfaced at launch: Professional, FancyGuy Consulting Engagements, Personal/Hobby
- Open source and reference architectures: placeholder cards acceptable at launch, full content Phase 2

**Post-Launch (Phase 2):**
- Open source contributions with GitHub integration
- Reference architecture pattern library
- In-progress / coming soon project cards
- Tag/category filtering (client-side)

**Individual Project Page — Blended Depth Model:**

Each project gets a page. Content depth is determined by what's available and appropriate, not forced into a single template:

| Project Type | Depth |
|---|---|
| Resume highlight (proprietary) | Card summary + context + tech tags |
| Consulting engagement | Case study format — problem → approach → outcome |
| Open source / passion project | Card + GitHub link + technical deep-dive + MDX posts as project evolves |
| Reference architecture | Architecture diagram + design rationale + pattern documentation |

**Required elements (all projects):**
- Name, category, year, tech tag chips (mono font)
- Description (minimum 2–3 sentences)
- Status badge: Complete / Active / In Progress / Coming Soon

**Optional elements (where available):**
- GitHub link
- Architecture diagram
- Case study narrative (MDX)
- Related blog posts
- Live demo link

**Constraints:**
- Proprietary work: descriptions and outcomes only, no source links
- MDX content stored in `/content/projects/[slug].mdx`
- Empty project pages must handle gracefully — no broken routes

### 3.4 Consulting & Services (`/consulting`)

**Purpose:** B2B lead generation surface for FancyGuy Technologies consulting engagements. Separate from Projects — this is forward-looking ("hire me for this") vs. Projects which is retrospective ("here's what I've built").

**Brand Relationship:**
FancyGuy Technologies operates as a DBA / practice name under the Steve Buzonas personal brand. buzonas.dev is the primary surface. The consulting section attributes work to FancyGuy Technologies as the operating entity without requiring a separate brand identity or domain. Framing: *"FancyGuy Technologies — Steve Buzonas, Principal Consultant."*

FancyGuy Technologies is a formal LLC — the name is fixed. This decision is captured in ADR-010. The DBA relationship is structural, not cosmetic: buzonas.dev is the personal brand surface; FancyGuy Technologies is the legal entity through which consulting engagements are contracted. If FancyGuy Technologies moves to its own domain in the future, `/consulting` content migrates with minimal rework.

**Required elements:**
- Practice overview — what FancyGuy Technologies does, who it serves
- Service offerings (defined by owner — placeholder at launch acceptable):
  - Cloud Architecture & Modernization
  - Platform Engineering & Kubernetes
  - DevOps / SRE Transformation
  - FinOps & Cost Optimization
  - Security & Compliance (HIPAA, zero-trust)
  - Engineering Leadership Advisory
- Reference Architecture pattern library (Phase 2 — cards with placeholder at launch)
- Select consulting engagement highlights (drawn from resume consulting section)
- CTA: Contact / Engage (links to contact section or email)

**Reference Architecture Pattern Library (Phase 2):**
- Each reference architecture gets a dedicated page (`/consulting/architectures/[slug]`)
- Format: architecture diagram + design rationale + service component breakdown + use case narrative
- Diagrams: Mermaid or SVG at launch; interactive (react-flow or similar) in Phase 3
- Examples: multi-account AWS landing zone, EKS multi-tenant platform, HIPAA-compliant workload pattern, GitOps deployment pipeline

**Constraints:**
- No pricing at launch — engagement model is conversation-driven
- Consulting section must not undermine the "available for full-time employment" signal elsewhere on the site — tone and placement matter
- FancyGuy Technologies name used consistently; never "my company" or unnamed

### 3.5 Competencies / Skills

**Purpose:** Scannable skills surface for recruiters; signal depth to engineers.

**Required elements:**
- Organized by domain (Cloud, Kubernetes & Platform, DevOps/SRE, Security, FinOps, Languages, IaC, Observability)
- Current certifications prominently displayed: AWS Solutions Architect Professional, AWS DevOps Engineer Professional, AWS Security Specialty (all 2026–2029)
- Visual treatment distinguishes expert-level from proficient

**Constraints:** Not a keyword dump — curated to match seniority level

### 3.6 Veteran & Nonprofit Work

**Purpose:** Communicate the dimension of service beyond the technical career.

**Required elements:**
- Active VSO affiliations: VFW, American Legion, DAV, Marine Corps League, IAVA
- Veterans Corner nonprofit formation narrative (in progress — framed appropriately)
- Marine Corps service summary: Corporal, MWSS 471 Det A, 2004–2012
- Visual treatment: distinct from tech sections (Patriot Red accent per brand guide)

**Constraints:**
- Nonprofit is forming — language must be accurate, not overstated
- Military section tone: precise and factual, not sentimental

### 3.7 Land & Conservation

**Purpose:** Complete the human portrait. Differentiates from every other engineering portfolio.

**Required elements:**
- Personal narrative about Appalachian land stewardship and property
- Photography or illustration (TBD — placeholder at launch acceptable)
- Tone: warm and personal — the one section where warmth is fully appropriate

**Constraints:**
- Content TBD from owner; placeholder copy at launch acceptable

### 3.8 Blog (MDX)

**Purpose:** Demonstrate thinking, build audience, support SEO.

**Required elements:**
- Post listing page with category filter (Tech, Veteran, Nature/Conservation)
- Individual post pages with MDX rendering
- Category accent color per brand guide (sky = tech, patriot-red = veteran, pine = nature)
- Reading time estimate
- Author block (consistent)
- No comments at launch

**Constraints:**
- MDX files stored in `/content/blog/`
- Zero posts required at launch — empty state must be handled gracefully
- Reader Mode: Phase 2 (stripped, high-contrast reading experience)

### 3.9 Contact / Call to Action

**Purpose:** Convert interest into connection.

**Required elements:**
- Links to: LinkedIn, GitHub, email
- No contact form at launch (spam risk, no backend)
- Resume download (PDF)
- Location: Pulaski, PA (no street address)

---

## 4. Non-Functional Requirements

### 4.1 Performance
- Lighthouse Performance ≥ 90 (mobile and desktop)
- Core Web Vitals: LCP < 2.5s, CLS < 0.1, INP < 200ms
- Static export — no server-side runtime at launch

### 4.2 Accessibility
- WCAG 2.1 AA compliance
- Keyboard navigable
- Screen reader compatible
- `prefers-reduced-motion` respected for all animations

### 4.3 Responsive Design
- Mobile-first
- Breakpoints: sm (640px), md (768px), lg (1024px), xl (1280px)
- Hero silhouette must be visible and intentional at all breakpoints

### 4.4 Theming
- **Light mode and dark mode** required at launch
- System preference detection (`prefers-color-scheme`) as default
- Manual toggle in navigation
- Dark mode is the primary/designed experience; light mode is fully specified (not inverted)

### 4.5 Print Styling
- **Color print stylesheet** — preserves brand identity for printed resume/portfolio pages
- **Black & white print stylesheet** — high-contrast, ink-efficient for monochrome printers
- Print trigger: browser native print + optional "Print" button in contact section
- Navigation, hero animations, and decorative SVGs suppressed in print

### 4.6 SEO
- OpenGraph and Twitter card meta per page
- Structured data (JSON-LD) for person schema
- Sitemap generated at build
- `robots.txt` permissive

### 4.7 Analytics
- Privacy-respecting analytics (no cookies, no PII) — Phase 2
- No third-party tracking scripts at launch

---

## 5. Release Phases

### Phase 0 — Foundation (Pre-Launch Infrastructure)
- Repository setup and governance documentation
- **Design guide finalized — blocking dependency.** The visual identity (colors, typography, wordmark, signature composition elements) went through extensive iteration against the real Canva reference asset (2026-06-30 working session) and produced a draft proof (`brand-guidelines-showcase.html`, hand-built HTML/SVG). That draft validated the values but was explicitly not treated as production-ready — see the companion design-system PRD for the plan to formalize it into a living component playground. **Phase 1 MVP build does not start until this is finalized**, since brand tokens, the wordmark treatment, and the signature visual elements (hex lattice, hex-tiled mountain accent, treeline silhouette) are direct inputs to `globals.css`, `tailwind.config.ts`, and the `AppalachianSilhouette`/`HexLattice`/`HexGrid` components (see ADR-010, component-inventory.md §7.5–7.7).
- Tailwind + shadcn/ui configuration
- Brand tokens implemented
- CI/CD pipeline (GitHub Actions)
- Staging environment configured (GitHub Pages preview branch)
- `buzonas.dev` domain verified

### Phase 1 — MVP Launch

**In scope:**
- All home page sections (Hero, Experience, Competencies, Veteran, Land, Contact)
- `/projects` index — resume highlight cards (Professional + FancyGuy Engagements + Personal/Hobby)
- `/projects/[slug]` — individual project pages (description depth varies; no broken routes)
- `/consulting` — practice overview + service offerings (reference architectures placeholder)
- `/blog` — index + MDX post rendering (0 posts at launch; empty state handled gracefully)
- Dark mode + light mode (system default + manual toggle)
- Color and B&W print stylesheets
- Static export, GitHub Pages deploy
- Lighthouse ≥ 90 all categories
- WCAG 2.1 AA
- OpenGraph / SEO metadata + JSON-LD person schema
- Sitemap + robots.txt
- Resume PDF download
- Mobile responsive (375px → 1280px)

**Out of scope for MVP:**
- Blog post content
- Reference architecture pattern library (placeholder cards only)
- Open source project deep-dives
- Analytics
- Reader mode
- Project tag/category filtering
- Contact form
- `/consulting/architectures/[slug]` routes

### Phase 2 — Post-Launch Enhancements

| Feature | Priority | Notes |
|---------|----------|-------|
| Blog content (first posts) | High | Tech + veteran category each |
| Reader mode | High | Stripped MDX reading experience |
| Analytics (privacy-respecting) | Medium | Plausible or Fathom |
| Project tag filtering | Medium | Client-side filter |
| Nonprofit section expansion | Medium | As nonprofit formation progresses |
| Land section photography | Low | Real assets vs. placeholder |
| Contact form | Low | Requires serverless function or form service |

### Phase 3 — Growth

- RSS feed for blog
- Search (Pagefind — static search)
- Speaking/presentations section
- Open source contribution highlights

---

## 6. Staging & Release Process

### 6.1 Delivery Model

This project follows a **continuous delivery** model. `main` is always deployable. The staging environment receives every push automatically. Production promotion is an explicit, deliberate act via semantic version tag — not a branch gate.

```
commit → main → [CI: lint, build, test] → staging.buzonas.dev (automatic)
                                                    ↓
                              manual review / approval
                                                    ↓
                         git tag v1.x.x OR GitHub Release draft
                                                    ↓
                                          buzonas.dev (production)
```

### 6.2 Staging Environment

- **Source:** `main` branch — every push
- **Target:** `staging.buzonas.dev`
- **Trigger:** GitHub Actions on `push` to `main`
- **Purpose:** Continuous visual review; catch regressions before tagging a release
- **Expectation:** Staging may be ahead of production at any time — that is intentional

### 6.3 Production Deploy

- **Source:** Semantic version tag on `main` (e.g., `v1.0.0`)
- **Target:** `buzonas.dev`
- **Trigger:** Either method initiates the production pipeline:
  - `git tag v1.x.x && git push origin v1.x.x` (CLI)
  - GitHub Release UI — publish a draft release targeting `main`
- **Versioning:** Semantic versioning (`vMAJOR.MINOR.PATCH`)
  - `MAJOR` — breaking visual or structural change
  - `MINOR` — new section, feature, or significant content addition
  - `PATCH` — copy edits, bug fixes, minor style adjustments
- **Rollback:** Re-tag a previous commit and push, or re-publish a prior GitHub Release

### 6.4 CI Pipeline (Both Environments)

Every push to `main` and every release tag runs the full pipeline:

- [ ] TypeScript typecheck (`tsc --noEmit`)
- [ ] ESLint
- [ ] Next.js static build (`next build` with `output: 'export'`)
- [ ] Lighthouse CI (score gates: Performance ≥ 90, Accessibility ≥ 90, Best Practices ≥ 90, SEO ≥ 90)
- [ ] axe-core accessibility scan
- [ ] Dead link check

### 6.5 Pre-Release Checklist (GitHub PR Template)

Before tagging a production release, verify on staging:

- [ ] Lighthouse scores ≥ 90 (all four categories)
- [ ] Light and dark mode verified
- [ ] Print stylesheet verified (color + B&W)
- [ ] Mobile breakpoints verified (375px, 768px, 1280px)
- [ ] All links valid (no 404s)
- [ ] OpenGraph preview verified (use opengraph.xyz or similar)
- [ ] Accessibility scan clean (axe-core)
- [ ] `prefers-reduced-motion` verified
- [ ] Resume PDF download confirmed working

---

## 7. Out of Scope (All Phases)

- Backend / API server
- User authentication
- CMS (content is file-based MDX)
- E-commerce
- Job board integration
- Comments or community features

---

## 8. Open Questions

| # | Question | Owner | Status |
|---|----------|-------|--------|
| 1 | Land section — real photography or illustrated placeholder at launch? | Steve | **Resolved — illustrated placeholder at launch** |
| 2 | Nonprofit name confirmed for site use? | Steve | **Resolved — Veterans Corner** |
| 3 | Resume PDF — same plain version or designed PDF matching brand? | Steve | **Resolved — branded/stylized PDF ready** |
| 4 | Headshot — available and approved for use? | Steve | **Resolved — LinkedIn profile image available; needs editing before use** |
| 5 | Blog — any posts planned for launch, or clean empty state? | Steve | **Resolved — clean empty state at launch** |
| 6 | Reader mode — Phase 2 confirmed, or pull into MVP? | Steve | **Resolved — Phase 2, not in MVP** |

---

## 9. Stakeholders

| Role | Name |
|------|------|
| Product Owner | Steve Buzonas |
| Lead Engineer | Steve Buzonas |
| AI Coding Assistant | Claude (Anthropic) |
| Design Reference | buzonas-brand-guide.md |

---

*PRD v1.0 — buzonas.dev Portfolio Site*
*Next document: Technical Architecture Document*
