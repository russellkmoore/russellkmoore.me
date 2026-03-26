# Architecture Research

**Domain:** Personal portfolio site (static, content-driven)
**Researched:** 2026-03-26
**Confidence:** HIGH

## Standard Architecture

### System Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                     Build Layer (Astro)                          │
│                                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐       │
│  │  Pages   │  │ Layouts  │  │Components│  │  Styles  │       │
│  │ (routes) │  │ (shells) │  │  (UI)    │  │(Tailwind)│       │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘       │
│       │              │              │              │             │
│       └──────────────┴──────┬───────┴──────────────┘             │
│                             │                                   │
│  ┌──────────────────────────┴───────────────────────────────┐   │
│  │              Content Collections (data layer)             │   │
│  │         src/content/projects/  src/content/writing/       │   │
│  └──────────────────────────┬───────────────────────────────┘   │
│                             │                                   │
│                      astro build                                │
│                             │                                   │
├─────────────────────────────┼───────────────────────────────────┤
│                     Output: dist/                               │
│              Static HTML + CSS + minimal JS                     │
├─────────────────────────────┼───────────────────────────────────┤
│                   Cloudflare Workers                            │
│              (serves static assets via wrangler)                │
└─────────────────────────────────────────────────────────────────┘
```

### Component Responsibilities

| Component | Responsibility | Typical Implementation |
|-----------|----------------|------------------------|
| `Layout.astro` | HTML shell, head meta, nav, footer, global styles | Single layout wrapping all pages via `<slot />` |
| `src/pages/index.astro` | Single-page composition of all sections | Imports section components, queries collections |
| Section components | Render each major section (Hero, About, Projects, etc.) | `.astro` files, no client JS needed |
| Reusable UI components | Card, Badge, SectionHeader patterns | Pure `.astro` components with props |
| Content collections | Type-safe structured data for projects and writing | Markdown files with Zod-validated frontmatter |
| Tailwind config | Design tokens (colors, fonts, spacing) | `tailwind.config.mjs` with custom theme |
| Static assets | Fonts, favicons, OG images | `public/` directory, copied as-is to build |

## Recommended Project Structure

```
russellkmoore.me/
├── public/                     # Static assets (no processing)
│   ├── favicon.svg
│   └── og-image.png
├── src/
│   ├── components/             # Reusable UI components
│   │   ├── Card.astro          # Generic card (projects, writing, career)
│   │   ├── Badge.astro         # Tech stack tags, status indicators
│   │   ├── SectionHeader.astro # Section title + optional subtitle
│   │   ├── Nav.astro           # Sticky navigation bar
│   │   ├── Footer.astro        # Site footer
│   │   └── MobileMenu.astro   # Hamburger menu (only interactive piece)
│   ├── sections/               # Page section compositions
│   │   ├── Hero.astro          # Hero with CTAs and animated gradient
│   │   ├── About.astro         # Narrative + stats cards
│   │   ├── Projects.astro      # Card grid from content collection
│   │   ├── Career.astro        # Highlight cards (AT&T, Spark::red, Pivotree)
│   │   ├── Writing.astro       # Card grid or "coming soon" state
│   │   └── Contact.astro       # Email + LinkedIn + availability copy
│   ├── layouts/
│   │   └── Layout.astro        # Base HTML shell (head, nav, footer, slot)
│   ├── pages/
│   │   └── index.astro         # Single page: imports and composes sections
│   ├── content/                # Content collection source files
│   │   ├── projects/
│   │   │   ├── mercora.md
│   │   │   └── recompai.md
│   │   └── writing/
│   │       ├── article-1.md    # draft: true
│   │       ├── article-2.md    # draft: true
│   │       └── article-3.md    # draft: true
│   └── styles/
│       └── global.css          # Tailwind directives, font imports, base styles
├── src/content.config.ts       # Collection schemas (Astro 5+ location)
├── astro.config.mjs            # Astro configuration
├── tailwind.config.mjs         # Design tokens, custom theme
├── tsconfig.json               # TypeScript config
├── wrangler.toml               # Cloudflare Workers deployment config
└── package.json
```

### Structure Rationale

- **`components/` vs `sections/`:** Separating reusable UI primitives (Card, Badge) from page-level section compositions (Hero, Projects) prevents a flat folder of 15+ components with mixed abstraction levels. Sections import components; components never import sections.
- **`content/` with glob loader:** Astro 5+ uses `src/content.config.ts` at the project root of `src/` with glob loaders pointing to `src/content/`. Each subdirectory maps to one collection. This replaced the old `src/content/config.ts` convention.
- **Single `Layout.astro`:** One page, one layout. No need for multiple layout variants. The layout owns the HTML document shell, nav, and footer.
- **`styles/global.css`:** Houses `@tailwind` directives and any global overrides (font-face declarations, scrollbar styling, selection color). Most styling lives in Tailwind utility classes directly on elements.
- **`wrangler.toml` at root:** Cloudflare Workers configuration pointing to `dist/` as the static asset directory.

## Architectural Patterns

### Pattern 1: Single-Page Section Composition

**What:** `index.astro` imports section components and composes them vertically. Each section is self-contained with its own data fetching.
**When to use:** Single-page portfolio sites where all content appears on one scrollable page.
**Trade-offs:** Simple and fast to build. Adding new sections is trivial. Downside: all content loads at once (acceptable for a portfolio with limited content).

```astro
---
// src/pages/index.astro
import Layout from '../layouts/Layout.astro';
import Hero from '../sections/Hero.astro';
import About from '../sections/About.astro';
import Projects from '../sections/Projects.astro';
import Career from '../sections/Career.astro';
import Writing from '../sections/Writing.astro';
import Contact from '../sections/Contact.astro';
---

<Layout title="Russell Moore — Engineering Leader & Builder">
  <Hero />
  <About />
  <Projects />
  <Career />
  <Writing />
  <Contact />
</Layout>
```

### Pattern 2: Content Collections with Draft Filtering

**What:** Define typed schemas for content, query with `getCollection()`, filter drafts at build time so they never appear in output HTML.
**When to use:** Any structured content that needs consistent frontmatter and type safety.
**Trade-offs:** Excellent DX with autocomplete and build-time validation. Slight learning curve with the Astro 5+ content layer API.

```typescript
// src/content.config.ts
import { defineCollection } from 'astro:content';
import { glob } from 'astro/loaders';
import { z } from 'astro/zod';

const projects = defineCollection({
  loader: glob({ pattern: '**/*.md', base: './src/content/projects' }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    url: z.string().url().optional(),
    repo: z.string().url().optional(),
    techStack: z.array(z.string()),
    status: z.enum(['Live', 'In Development']),
    order: z.number(),
  }),
});

const writing = defineCollection({
  loader: glob({ pattern: '**/*.md', base: './src/content/writing' }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    pubDate: z.coerce.date(),
    draft: z.boolean().default(true),
  }),
});

export const collections = { projects, writing };
```

```astro
---
// Inside src/sections/Writing.astro
import { getCollection } from 'astro:content';

const publishedPosts = await getCollection('writing', ({ data }) => !data.draft);
const hasContent = publishedPosts.length > 0;
---

{hasContent ? (
  <!-- render card grid -->
) : (
  <!-- render "coming soon" state -->
)}
```

### Pattern 3: Props-Driven Reusable Components

**What:** UI components accept typed props, render deterministically, ship zero JS. Use Astro's built-in component props with TypeScript interfaces.
**When to use:** Any UI element used more than once (Card, Badge, SectionHeader).
**Trade-offs:** No runtime overhead. Cannot hold client-side state (not needed here).

```astro
---
// src/components/Card.astro
interface Props {
  title: string;
  description: string;
  tags?: string[];
  href?: string;
}

const { title, description, tags = [], href } = Astro.props;
---

<article class="rounded-xl border border-white/10 bg-white/5 p-6">
  <h3 class="text-lg font-semibold text-white">{title}</h3>
  <p class="mt-2 text-sm text-gray-400">{description}</p>
  {tags.length > 0 && (
    <div class="mt-4 flex flex-wrap gap-2">
      {tags.map(tag => <Badge label={tag} />)}
    </div>
  )}
</article>
```

## Data Flow

### Build-Time Content Flow

```
Markdown files (src/content/projects/*.md, src/content/writing/*.md)
    │
    ↓ (glob loader reads at build time)
Content Collections (validated by Zod schemas in content.config.ts)
    │
    ↓ (getCollection() queries in section components)
Section Components (Projects.astro, Writing.astro)
    │
    ↓ (filter, sort, map over entries)
Reusable Components (Card.astro, Badge.astro)
    │
    ↓ (Astro renders to static HTML)
dist/ (static HTML + CSS, zero JS unless MobileMenu needs it)
    │
    ↓ (wrangler deploy)
Cloudflare Workers (serves static files globally)
```

### Key Data Flows

1. **Content to UI:** Markdown frontmatter flows through Zod validation into typed `data` objects, consumed by section components via `getCollection()`, then passed as props to Card/Badge components. All at build time -- zero runtime queries.
2. **Navigation scroll:** Nav links use `href="#section-id"` anchors. Each section component renders with an `id` attribute. Smooth scrolling handled by CSS `scroll-behavior: smooth` on `<html>`. No JS router needed.
3. **Mobile menu toggle:** The only interactive element. A small inline `<script>` toggles a CSS class on the menu element. This is the one place where client-side JS exists, and it should be minimal (under 20 lines).
4. **Static data (Career, About, Contact):** Hardcoded in their section components or stored as simple TypeScript objects in the component frontmatter. No need for content collections for data that changes rarely.

### State Management

None needed. This is a fully static site with no client-side state. All "state" is resolved at build time through content collections and component props. The mobile menu toggle is the only runtime behavior and can be handled with a `<script>` tag and DOM manipulation -- no framework needed.

## Scaling Considerations

| Scale | Architecture Adjustments |
|-------|--------------------------|
| Current (single page) | All sections on index.astro, no routing complexity |
| Adding blog pages | Add `src/pages/writing/[slug].astro` for individual post pages, content already in collections |
| Adding more project detail pages | Add `src/pages/projects/[slug].astro`, same pattern |
| Many posts (50+) | Add pagination via Astro's `paginate()` in `getStaticPaths()` |

### Scaling Priorities

1. **First evolution:** Individual blog post pages. The content collection is already structured for this -- just add a dynamic route page. No architecture change needed.
2. **Second evolution:** Search/filtering on projects or writing. Would require adding a small client-side JS island (Astro islands architecture) for interactive filtering without page reload.

## Anti-Patterns

### Anti-Pattern 1: Using a UI Framework for Static Content

**What people do:** Import React/Vue/Svelte components for cards, sections, and layout elements that have zero interactivity.
**Why it's wrong:** Ships unnecessary JavaScript to the client. Astro components render to pure HTML with zero JS overhead. Using a framework component means you need to decide on hydration strategy (`client:load`, `client:visible`, etc.) for something that never needs hydration.
**Do this instead:** Use `.astro` components for everything. Reserve framework components only for truly interactive elements (and this portfolio has none beyond the mobile menu toggle).

### Anti-Pattern 2: Over-Engineering Content Collections

**What people do:** Create content collections for career highlights, about section stats, contact info -- data that changes once a year.
**Why it's wrong:** Content collections add indirection (schema definition, loader config, query calls) that is only justified when content is numerous, changes frequently, or follows a repeating pattern. Static data for 3 career cards does not warrant this overhead.
**Do this instead:** Use content collections for projects and writing (many items, consistent schema). Hardcode career highlights, about stats, and contact info directly in their section components or as simple TypeScript constants.

### Anti-Pattern 3: Putting Styles in a Separate CSS File Per Component

**What people do:** Create `Hero.css`, `Card.css`, etc. alongside each component.
**Why it's wrong:** With Tailwind, styling lives in utility classes on the markup. Separate CSS files create a parallel style system that conflicts with the utility-first approach and makes maintenance harder.
**Do this instead:** Use Tailwind utility classes in component markup. Use `global.css` only for Tailwind directives, font imports, and truly global styles (scrollbar, selection). Use Astro's `<style>` tag (scoped by default) only when a one-off style truly cannot be expressed in Tailwind.

### Anti-Pattern 4: Using @astrojs/cloudflare for Static Output

**What people do:** Install the Cloudflare adapter even when the entire site is statically generated.
**Why it's wrong:** The `@astrojs/cloudflare` adapter is designed for on-demand server rendering. For purely static sites, it adds unnecessary complexity and there are known issues with `output: "static"` combined with the adapter (see [GitHub issue #15650](https://github.com/withastro/astro/issues/15650)). The adapter defaults to `output: 'server'`.
**Do this instead:** Build with default static output (`astro build` produces `dist/`), then deploy to Cloudflare Workers using `wrangler deploy` with a `wrangler.toml` that points to `dist/` as the assets directory. No adapter needed for static sites.

## Integration Points

### External Services

| Service | Integration Pattern | Notes |
|---------|---------------------|-------|
| Cloudflare Workers | `wrangler deploy` pointing to `dist/` | Static asset serving, no adapter needed for static output |
| Cloudflare DNS | A/CNAME records for russellkmoore.me | Already configured per PROJECT.md |
| @fontsource/geist-sans | npm package imported in global.css | Self-hosted font, no external requests |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| Layout <-> Sections | `<slot />` for content injection | Layout provides nav/footer shell, sections fill the slot |
| Sections <-> Components | Props (typed interfaces) | Sections pass data down as typed props to Card, Badge, etc. |
| Sections <-> Content | `getCollection()` API | Only Projects and Writing sections query content collections |
| Content Config <-> Markdown | Zod schema validation | Build fails fast if frontmatter doesn't match schema |
| Nav <-> Sections | Anchor links (`#section-id`) | Each section renders an `id`, nav links scroll to it |

## Build Order (Dependency Graph)

The following reflects what must exist before other pieces can work:

```
Phase 1: Foundation (no dependencies)
  ├── astro.config.mjs
  ├── tailwind.config.mjs (design tokens: colors, fonts)
  ├── tsconfig.json
  ├── global.css (Tailwind directives, font imports)
  └── Layout.astro (HTML shell with <slot />)

Phase 2: Reusable Components (depends on: Tailwind config for design tokens)
  ├── SectionHeader.astro
  ├── Card.astro
  ├── Badge.astro
  ├── Nav.astro
  └── Footer.astro

Phase 3: Content Layer (depends on: nothing, but needed before sections that use it)
  ├── src/content.config.ts (schemas for projects + writing)
  ├── src/content/projects/mercora.md
  ├── src/content/projects/recompai.md
  └── src/content/writing/*.md (draft: true)

Phase 4: Sections (depends on: components + content layer)
  ├── Hero.astro (depends on: Layout tokens only)
  ├── About.astro (depends on: Card, SectionHeader)
  ├── Projects.astro (depends on: Card, Badge, content collections)
  ├── Career.astro (depends on: Card, SectionHeader)
  ├── Writing.astro (depends on: Card, content collections)
  └── Contact.astro (depends on: SectionHeader)

Phase 5: Page Assembly (depends on: all sections)
  └── index.astro (imports Layout + all sections)

Phase 6: Deployment (depends on: working build)
  ├── wrangler.toml
  └── npm scripts (dev, build, deploy)
```

## Sources

- [Astro Project Structure docs](https://docs.astro.build/en/basics/project-structure/)
- [Astro Content Collections docs](https://docs.astro.build/en/guides/content-collections/)
- [Astro Layouts docs](https://docs.astro.build/en/basics/layouts/)
- [Astro Components docs](https://docs.astro.build/en/basics/astro-components/)
- [Deploy Astro to Cloudflare docs](https://docs.astro.build/en/guides/deploy/cloudflare/)
- [Cloudflare adapter static output issue #15650](https://github.com/withastro/astro/issues/15650)
- [Astro Content Collections API Reference](https://docs.astro.build/en/reference/modules/astro-content/)

---
*Architecture research for: russellkmoore.me personal portfolio*
*Researched: 2026-03-26*
