# Phase 2: Components & Content - Context

**Gathered:** 2026-03-26
**Status:** Ready for planning

<domain>
## Phase Boundary

Complete, responsive single-page portfolio with hero, about, projects, career, writing, and contact sections — all with real content. Builds on Phase 1's Astro scaffold, dark theme, fonts, and content collections.

</domain>

<decisions>
## Implementation Decisions

### Hero & First Impression
- **D-01:** Tagline tone is impact-focused — something like "Building products at the intersection of AI and commerce" (not the generic "Engineering Leader & Builder" from the placeholder)
- **D-02:** Subtle mesh gradient background behind hero — slow-moving color blobs (violet + dark blue), barely noticeable but adds depth. Think vercel.com hero energy
- **D-03:** CTA buttons are text links with arrow icons, not buttons — "See My Work →" and "Let's Talk →". Minimalist, linear.app energy
- **D-04:** Hero height ~60-70vh — generous but not full viewport. Top of About section peeks above the fold, inviting scroll

### Section Flow & Navigation
- **D-05:** Section order: Hero → About → Projects → Career → Writing → Contact (story flow: who you are → what you've built → where you've been → what you think → how to reach you)
- **D-06:** Sticky nav transitions from transparent over hero to solid dark background on scroll (smooth CSS transition)
- **D-07:** Nav contains section text links only: About, Projects, Career, Writing, Contact (right side). Name/logo on left. No resume button
- **D-08:** Smooth scroll behavior (CSS scroll-behavior: smooth) for all anchor links

### Content Presentation
- **D-09:** Projects use featured card + list layout — featured projects (Mercora, RecompAI) get larger hero-style cards. Non-featured projects in a compact list below. Visual hierarchy by importance
- **D-10:** About section: narrative paragraph above, 4 stats cards row below. Stats: 20+ Years, $2B+ Platform Scale, $13M ARR Built & Acquired, 200+ Global Team
- **D-11:** Career highlights in timeline style — vertical timeline with connected dots/line. Each entry has company (AT&T, Spark::red, Pivotree), role context, and key quantified metrics
- **D-12:** Contact section as centered CTA block — brief "what I'm open to" paragraph centered, email and LinkedIn as text links with arrow icons below

### Visual Separation
- **D-13:** Gradient fade dividers between sections — soft gradient fades (dark → slightly lighter → dark). More atmospheric than plain lines or whitespace only

### Responsive Strategy
- **D-14:** Mobile-first approach using Tailwind's sm:/md:/lg: modifiers
- **D-15:** Mobile nav: hamburger triggers slide-in panel from the right with section links stacked vertically
- **D-16:** Grid behavior on mobile: Claude's discretion per section based on content density

### About Narrative
- **D-17:** First-person, direct voice — "I build AI-powered products and lead engineering teams..." Personal, confident, approachable

### Claude's Discretion
- Footer layout (minimal, satisfying LAYOUT-03 with copyright + LinkedIn + GitHub icons)
- Grid breakpoint behavior per section (mobile stacking strategy)
- Exact gradient animation CSS (mesh gradient approach)
- Section heading component implementation details
- Badge and Card component internal styling
- Active nav link highlighting approach

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Phase 1 foundation (established patterns)
- `src/layouts/Layout.astro` — Current Layout with font imports, FOUC prevention, Props interface
- `src/styles/global.css` — Tailwind v4 @theme tokens (background, foreground, muted, accent, font-sans)
- `src/content.config.ts` — Content collection schemas (projects, writing) with Zod validation
- `src/pages/index.astro` — Current proof-of-concept page (will be replaced with full sections)
- `astro.config.mjs` — Astro config with @tailwindcss/vite plugin

### Phase 1 UI design contract
- `.planning/phases/01-foundation-scaffolding/01-UI-SPEC.md` — Spacing scale, typography scale, color tokens, visual hierarchy

### Project requirements
- `.planning/REQUIREMENTS.md` — LAYOUT-01 through CONTACT-02 requirements for this phase
- `.planning/PROJECT.md` — Project vision, constraints, out-of-scope items

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `Layout.astro` — Wraps pages with head/fonts/FOUC prevention. Needs nav/footer added (LAYOUT-01)
- `global.css` — Theme tokens already defined: --color-background, --color-foreground, --color-muted, --color-accent, --color-accent-hover, --font-sans
- Content collections (projects, writing) — Already schema-validated and queryable via getCollection()
- Existing index.astro project rendering pattern — `border-white/10 bg-white/5` card style, `bg-accent/20 text-accent` tag style

### Established Patterns
- Tailwind v4 CSS-first config with @theme directive (no JS config)
- Font loading via @fontsource imports in Layout.astro frontmatter
- Dark theme via hardcoded `class="dark"` on html element
- FOUC prevention via `is:inline` style in head

### Integration Points
- Layout.astro `<slot />` — All section content flows through this
- Content collections — getCollection("projects"), getCollection("writing") already work
- No existing components directory — all components are greenfield

</code_context>

<specifics>
## Specific Ideas

- Design energy: linear.app / vercel.com — dark, clean, modern, generous whitespace (carried from Phase 1)
- No heavy animations — explicit out-of-scope. CSS transitions only
- Hero gradient should be subtle enough that text remains highly readable
- Text link CTAs with arrows match the minimalist linear.app aesthetic better than buttons
- Career timeline should have real quantified metrics, not vague descriptions
- "Coming soon." for writing section (draft filtering already works from Phase 1)

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 02-components-content*
*Context gathered: 2026-03-26*
