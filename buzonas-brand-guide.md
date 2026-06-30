# Steve Buzonas — Portfolio Brand Guide
**Version 1.0 | buzonas.dev**

---

## Brand Concept

**"Where the network meets the mountain."**

The visual identity bridges two worlds that genuinely coexist in Steve's life: the precision and scale of enterprise cloud engineering, and the grounded, rooted character of Appalachian Pennsylvania. The aesthetic is cinematic and executive — not flashy, not corporate-sterile. It says: this person has done serious work, and has a life beyond it.

The patriotic thread is present but never performative. It's embedded in the color discipline and texture — felt before it's named.

---

## Color System

### Primary Palette

| Token | Name | Hex | Usage |
|-------|------|-----|-------|
| `--color-void` | Void Navy | `#0A0F1E` | Page background, deepest shadows |
| `--color-deep` | Deep Navy | `#0D1B2A` | Section backgrounds, card base |
| `--color-steel` | Steel Blue | `#1B3A5C` | Primary surface, nav background |
| `--color-sky` | Sky Mist | `#A8D4E6` | Primary accent, links, highlights |
| `--color-glacier` | Glacier | `#C9E8F5` | Light accent, hover states |
| `--color-white` | Cloud White | `#EFF6FB` | Body text on dark backgrounds |

### Secondary / Accent Palette

| Token | Name | Hex | Usage |
|-------|------|-----|-------|
| `--color-patriot` | Patriot Red | `#8B1A2B` | Subtle accent only — VSO/military section, hover micro-moments |
| `--color-iron` | Iron | `#2E3F50` | Dividers, secondary surfaces |
| `--color-pine` | Pine Shadow | `#1A2B1E` | Tree silhouette SVG, conservation section |
| `--color-mist` | Mountain Mist | `#7BA7BC` | Mid-tone, secondary text, labels |

### Gradient Definitions

```css
/* Hero background — executive base */
--gradient-hero: linear-gradient(160deg, #0A0F1E 0%, #1B3A5C 55%, #A8D4E6 100%);

/* Section transition */
--gradient-section: linear-gradient(180deg, #0D1B2A 0%, #0A0F1E 100%);

/* Accent glow — used sparingly on interactive elements */
--gradient-accent: linear-gradient(135deg, #A8D4E6 0%, #C9E8F5 100%);
```

### Color Discipline

- **Never use Patriot Red as a primary action color.** It exists only as a whisper — a border on a veteran section card, a subtle underline on a Marine Corps reference. Overuse destroys the effect.
- **The sky/glacier range is your workhorse accent.** It reads as both "cloud" (technical) and "Pennsylvania sky" (roots) without being explained.
- **Background hierarchy:** Void Navy → Deep Navy → Steel Blue. Never reverse this layering.

---

## Typography

### Type Stack

| Role | Typeface | Weight | Notes |
|------|----------|--------|-------|
| **Display / Hero** | `Inter` or `Plus Jakarta Sans` | 700–800 | Large, wide-tracked, caps for labels |
| **Body** | `Inter` | 400–500 | Clean, readable at all sizes |
| **Mono / Code** | `JetBrains Mono` | 400–500 | Terminal aesthetic; used in tech sections, project tags |
| **Caption / Label** | `Inter` | 400, uppercase, tracked | Section eyebrows, metadata |

> **Google Fonts import:** `Plus Jakarta Sans` (300,400,600,700,800) + `JetBrains Mono` (400,500)

### Type Scale (Tailwind + CSS custom properties)

```css
--text-hero:    clamp(2.5rem, 6vw, 5rem);      /* Hero name */
--text-display: clamp(1.75rem, 3.5vw, 3rem);   /* Section headers */
--text-title:   clamp(1.25rem, 2vw, 1.75rem);  /* Card titles, role titles */
--text-body:    1rem;                            /* 16px baseline */
--text-small:   0.875rem;                        /* Labels, captions */
--text-mono:    0.85rem;                         /* Code, tags */
```

### Type Behaviors

- Hero name: large, white, weight 800, generous letter-spacing (`0.02em`)
- Role/title line: `--color-sky`, weight 400, slightly smaller — the accent that names the category
- Section eyebrows: `JetBrains Mono`, uppercase, `--color-mist`, tracked wide (`0.15em`) — reads like a terminal label
- Body copy: `--color-white` at 90% opacity on dark surfaces for depth

---

## Signature Visual Elements

### 1. The Geometric Network (Left Anchor)
Hexagonal lattice SVG — represents cloud/platform engineering, distributed systems. Lives in the upper-left of the hero and select section backgrounds. Rendered in `--color-sky` at 8–15% opacity. **Never filled, always outline-only.**

### 2. The Appalachian Silhouette (Lower Third)
SVG treeline — Pennsylvania conifers and rolling ridge. Dark navy (`#0D1B2A` to `#1A2B1E`). Sits at the hero bottom and transitions the page into the first content section. This is the single most distinctive brand element. It tells the story before a word is read.

### 3. The Hex Grid (Right Accent)
Smaller hex pattern, upper-right corner, `--color-glacier` at 10% opacity. Creates bookend symmetry with the network lattice. Reinforces the technical identity without competing with content.

### 4. Patriotic Texture (Ultra-Subtle)
Very faint fabric/weave texture in the upper hero area — at 3–5% opacity max. If you can clearly see it, it's too strong. It should be felt, not seen. Use a CSS `background-blend-mode: overlay` approach rather than a visible image.

---

## Layout System

### Spacing Scale
```css
--space-xs:  0.5rem;   /* 8px */
--space-sm:  1rem;     /* 16px */
--space-md:  2rem;     /* 32px */
--space-lg:  4rem;     /* 64px */
--space-xl:  6rem;     /* 96px */
--space-2xl: 10rem;    /* 160px — section breathing room */
```

### Grid
- **Container max-width:** 1280px, centered, `px-6` mobile / `px-12` desktop
- **Content column:** max 72ch for body text (readability)
- **Card grid:** 1 col mobile → 2 col tablet → 3 col desktop
- **Hero:** Full viewport height, flex column centered, silhouette anchored to bottom

### Border Radius
```css
--radius-sm:  4px;    /* Tags, badges */
--radius-md:  8px;    /* Cards */
--radius-lg:  16px;   /* Feature cards */
--radius-full: 9999px; /* Pills */
```

---

## Component Patterns

### Section Eyebrow
```
[ TERMINAL_LABEL ]  ←  JetBrains Mono, uppercase, sky-mist color, wide-tracked
Section Title Here  ←  Display weight, white
Supporting sentence in body weight below.
```

### Card — Technical Project
- Dark surface (`--color-deep`)
- Top border accent in `--color-sky` (2px)
- Tag chips in mono font, steel background
- Hover: subtle `--color-sky` glow via `box-shadow`

### Card — Veteran / VSO
- Same structure, but top border accent in `--color-patriot` (red)
- Subtle texture layer on card background
- Never mix tech card style with VSO card style in the same row

### Timeline Entry (Career)
- Left-border line in `--color-steel`
- Active/current role: `--color-sky` left border
- Year label: mono font, mist color
- Company: display weight white
- Role: sky accent color

### Blog / MDX Post Preview
- Clean, minimal — this is where the design steps back
- Serif display optional for post titles only (differentiates blog from tech content)
- Category tag drives accent color (tech = sky, veteran = patriot-red, nature/conservation = pine)

---

## Motion & Animation

### Principles
- **Purposeful, not decorative.** One orchestrated entrance sequence on the hero. Everything else is subtle.
- **Respect `prefers-reduced-motion`** — all animations wrapped in media query.

### Hero Entrance Sequence
1. Background gradient fades in (300ms)
2. Tree silhouette rises from bottom (600ms, ease-out, slight upward translate)
3. Geometric network fades in left (400ms, 200ms delay)
4. Name/title text fades up (500ms, 400ms delay)
5. CTA buttons fade in (400ms, 700ms delay)

### Micro-interactions
- Nav links: `--color-sky` underline slides in from left on hover (200ms)
- Cards: 2px upward translate + sky glow on hover (150ms ease)
- Tech tags: background fill on hover (100ms)

---

## Voice & Tone

### The Narrative Arc
Steve's story is: **military discipline → early cloud pioneer → enterprise architect at scale → servant leader in veteran communities → steward of the land he loves.**

Each section of the site contributes a chapter. The voice should feel like the person who wrote a fraud rule DSL compiler AND planted trees on his property AND served in the Marines — someone with range, depth, and no interest in performing any of it.

### Tone by Section

| Section | Tone |
|---------|------|
| Hero | Confident, minimal. Let the work speak. |
| Experience | Precise and results-oriented. Metrics when available. |
| Projects | Curious, builder's pride. Show the thinking. |
| Veteran Work | Respectful, purposeful. Service without sentimentality. |
| Land Conservation | Personal, grounded. The one place to let warmth show fully. |
| Blog | Peer-to-peer. Write like you're at a whiteboard. |

### Copy Principles
- Lead with outcomes, not responsibilities
- Use the specific, not the general ("19 EKS clusters" > "large-scale Kubernetes")
- Never use the word "passionate"
- Avoid the phrase "results-driven"
- The military section uses the same precision as the tech sections — it's not a different register

---

## Tailwind + shadcn/ui Configuration Notes

### `tailwind.config.ts` Extensions
```typescript
extend: {
  colors: {
    void:    '#0A0F1E',
    deep:    '#0D1B2A',
    steel:   '#1B3A5C',
    sky:     '#A8D4E6',
    glacier: '#C9E8F5',
    mist:    '#7BA7BC',
    patriot: '#8B1A2B',
    iron:    '#2E3F50',
    pine:    '#1A2B1E',
    cloud:   '#EFF6FB',
  },
  fontFamily: {
    display: ['Plus Jakarta Sans', 'sans-serif'],
    body:    ['Inter', 'sans-serif'],
    mono:    ['JetBrains Mono', 'monospace'],
  },
}
```

### shadcn/ui Theme Overrides
- Set `--background` to `void` (#0A0F1E) — full dark theme
- Set `--primary` to `sky` (#A8D4E6)
- Set `--muted` to `iron` (#2E3F50)
- Set `--accent` to `glacier` (#C9E8F5)
- Card component: override default white background to `deep`

---

## Site Section Map

| Route | Section | Accent Color | Notes |
|-------|---------|--------------|-------|
| `/` | Hero + Nav | Sky | Full viewport, silhouette, network lattice |
| `/#experience` | Career Timeline | Sky | 20+ year arc, with military at the foundation |
| `/#projects` | Technical Projects | Sky | FancyGuy, Octothorpe, Course Map, ML work |
| `/#veteran` | Veteran & Nonprofit | Patriot Red | VFW, AL, DAV, MCL, IAVA, nonprofit formation |
| `/#land` | Land & Conservation | Pine | Appalachian stewardship, property, nature |
| `/blog` | Blog (MDX) | Context-driven | Tech, veteran, nature categories |
| `/about` | Full Bio (optional) | Sky | Longer-form narrative if needed |

---

## The Meta-Story (Portfolio as Project)

The site itself is a portfolio piece. The README and site should make this explicit:

> "This portfolio was built as a working prototype — using the same tools, workflow, and iterative process I'd apply to any production system. The stack (Next.js App Router, shadcn/ui, MDX, GitHub Actions → GitHub Pages) was chosen deliberately to showcase modern frontend engineering practice alongside cloud-native thinking."

This framing speaks directly to engineering leadership hiring managers: it shows process, judgment, and the ability to ship.

---

*Brand Guide v1.0 — Generated from visual assets and resume for buzonas.dev*
