# Phase 7: MDX Component Library - Context

**Gathered:** 2026-03-27
**Status:** Ready for planning

<domain>
## Phase Boundary

Build 6 reusable MDX components for use across writing and project detail pages. All components must work with the existing dark theme, use vanilla JS for interactivity (no React/Vue/Svelte), and be importable in .mdx files.

</domain>

<decisions>
## Implementation Decisions

### ScreenshotCarousel
- **D-01:** Horizontal scroll with CSS scroll-snap + optional arrow navigation
- **D-02:** Click image to open in lightbox (full-screen overlay with close button)
- **D-03:** Captions below each image (from alt text or explicit caption prop)
- **D-04:** Works for both projects (product screenshots) and writing (diagram/illustration sequences)

### InteractiveScreenshot
- **D-05:** Base image with positioned hotspot indicators (pulsing dots with numbers)
- **D-06:** Hover/focus on hotspot shows tooltip card (same pattern as timeline milestones — JS fixed-position)
- **D-07:** Mobile: tap to toggle tooltip (no hover on touch devices)
- **D-08:** Hotspots defined via props array: `[{ x: number, y: number, label: string, detail: string }]`

### Callout
- **D-09:** 4 variants: info (blue), warning (amber), tip (green), note (muted/gray)
- **D-10:** Left border accent + icon + content area. Dark theme appropriate colors
- **D-11:** Usage: `<Callout type="tip">Content here</Callout>`

### CodeComparison
- **D-12:** Side-by-side code blocks with "Before" / "After" labels (or custom labels)
- **D-13:** Stacks vertically on mobile
- **D-14:** Uses existing Shiki syntax highlighting (github-dark-default theme)

### Figure
- **D-15:** Image with optional caption, optional border/shadow, optional width control
- **D-16:** Caption renders as `<figcaption>` for semantic HTML
- **D-17:** Usage: `<Figure src="/path/to/image.png" caption="Dashboard overview" />`

### LinkCard
- **D-18:** Rich preview card for external links — shows title, description, and domain
- **D-19:** Fetches metadata at build time (not runtime) for static output compatibility
- **D-20:** Fallback: if metadata unavailable, render as styled link with URL domain

### All Components
- **D-21:** All vanilla JS + Astro components. No React/Vue/Svelte
- **D-22:** All respect dark theme tokens from global.css
- **D-23:** All components go in `src/components/mdx/` directory
- **D-24:** Export barrel file for easy importing in MDX

### Claude's Discretion
- Exact colors for callout variants
- Lightbox animation style
- Hotspot pulse animation CSS
- CodeComparison layout breakpoint
- LinkCard metadata extraction approach
- Whether to auto-register components globally or require explicit imports per MDX file

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Existing patterns
- `src/components/timeline/MilestoneNode.astro` — Hotspot + tooltip pattern (JS fixed-position)
- `src/components/timeline/TimelineBar.astro` — Scroll container + vanilla JS interaction
- `src/components/Card.astro` — Card styling pattern (border-white/10, bg-white/5)
- `src/styles/global.css` — Theme tokens, prose overrides, Shiki theme

### Configuration
- `astro.config.mjs` — MDX + Shiki already configured
- `src/content.config.ts` — Content collections with MDX support

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- Card component pattern (rounded-lg, border, bg-white/5)
- Timeline tooltip JS pattern (fixed positioning, viewport clamping)
- Prose typography overrides in global.css

### Established Patterns
- Vanilla JS in `<script>` tags for interactivity
- Tailwind utility classes for styling
- `is:inline` for styles that must stay in HTML

### Integration Points
- MDX files in `src/content/projects/` and `src/content/writing/`
- Components imported at top of MDX files

</code_context>

<specifics>
## Specific Ideas

- Components should feel native to the dark theme — not like embedded widgets
- Lightbox should be simple dark overlay, not a heavy library
- Callouts should match the linear.app/vercel.com energy — subtle, not loud
- CodeComparison is especially useful for writing about refactoring or migration patterns

</specifics>

<deferred>
## Deferred Ideas

- Video embed component (would need to decide on hosting — YouTube, self-hosted, etc.)
- Table of contents component (for long writing posts)
- Mermaid diagram rendering in MDX

</deferred>

---

*Phase: 07-mdx-component-library*
*Context gathered: 2026-03-27*
