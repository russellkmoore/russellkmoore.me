# Phase 3: Polish & Production - Context

**Gathered:** 2026-03-26
**Status:** Ready for planning

<domain>
## Phase Boundary

SEO metadata, Open Graph social previews, semantic HTML/accessibility polish, Lighthouse 95+ scores, and hero motion enhancement. No new sections or features — this is production readiness for the existing site.

</domain>

<decisions>
## Implementation Decisions

### OG Image & Social Previews
- **D-01:** OG image is screenshot-based — take a styled screenshot of the hero section at 1200x630, save as public/og-image.png. Needs manual updating if hero changes
- **D-02:** OG tags in Layout.astro head: og:title, og:description, og:image, og:type (website), og:url. Also twitter:card (summary_large_image)

### SEO Metadata
- **D-03:** Canonical URL via `<link rel="canonical">` pointing to https://russellkmoore.me/ (or configured site URL from astro.config.mjs)
- **D-04:** Page title already set from Phase 1 — verify it's "Russell Moore — Engineering Leader & Builder"
- **D-05:** Meta description already set from Phase 1 — verify it's appropriate for social sharing

### Hero Motion Enhancement
- **D-06:** Speed up hero mesh gradient animation from 20s+ to 8-12s cycle. Increase gradient blob size and opacity slightly for more noticeable movement. CSS-only change to existing Hero.astro keyframes

### Accessibility
- **D-07:** Claude's discretion for all accessibility work — proper heading hierarchy (h1 → h2 → h3), ARIA labels where needed, keyboard navigation, skip-to-content link, focus visible styles. Target WCAG AA compliance without over-engineering

### Claude's Discretion
- Exact heading hierarchy adjustments across sections
- Skip-to-content link implementation
- Focus visible styles approach
- Sitemap generation via @astrojs/sitemap (already in CLAUDE.md stack)
- Any additional Lighthouse optimization (image formats, unused CSS, render-blocking resources)

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Existing implementation (modify, don't replace)
- `src/layouts/Layout.astro` — Current head with title + meta description. Add OG tags and canonical URL here
- `src/components/sections/Hero.astro` — Current mesh gradient animation. Speed up keyframes here
- `src/styles/global.css` — Add focus-visible styles and skip-link styles here
- `astro.config.mjs` — Add @astrojs/sitemap integration here

### Phase 1 UI design contract
- `.planning/phases/01-foundation-scaffolding/01-UI-SPEC.md` — Color tokens, spacing, typography constraints

### Project requirements
- `.planning/REQUIREMENTS.md` — SEO-01, SEO-02, SEO-03 for this phase

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- Layout.astro already has Props interface with title + description — extend for OG tags
- Nav.astro and Footer.astro already have ARIA labels on interactive elements
- Hero.astro has `aria-hidden="true"` on decorative gradient div

### Established Patterns
- Tailwind v4 CSS-first config with @theme tokens
- Astro component Props interface pattern
- FOUC prevention via is:inline style in head

### Integration Points
- astro.config.mjs integrations array — add @astrojs/sitemap here
- Layout.astro `<head>` — all meta/OG tags go here
- public/ directory — OG image goes here

</code_context>

<specifics>
## Specific Ideas

- Hero motion feedback from Phase 2 checkpoint: "could have a little more motion" — addressed by D-06
- OG image should capture the hero's visual impact (gradient, name, tagline)
- Lighthouse 95+ is the hard target — optimize until it's met
- No heavy animation libraries (out of scope from project constraints)

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 03-polish-production*
*Context gathered: 2026-03-26*
