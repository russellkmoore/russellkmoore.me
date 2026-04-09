# Phase 5: Writing/Blog System - Context

**Gathered:** 2026-03-27
**Status:** Ready for planning

<domain>
## Phase Boundary

Transform the "Coming soon" Writing section into a full blog system with individual post pages at SEO-friendly URLs, a writing index page, and homepage section showing latest posts. Content authored in MDX with syntax-highlighted code blocks.

</domain>

<decisions>
## Implementation Decisions

### Post Page Layout
- **D-01:** Full-width immersive layout — hero gradient/image spanning full width at top, then centered content column below. Dramatic, good for thought leadership
- **D-02:** No reading time estimate — just date and tags. Keep it clean
- **D-03:** Post pages at `/writing/[slug]` — SEO-friendly unique URLs generated from content collection

### Content Format
- **D-04:** MDX format — markdown with embedded Astro/React components. Requires @astrojs/mdx integration. Enables callout boxes, interactive elements in posts
- **D-05:** Syntax-highlighted code blocks with dark theme — Astro's built-in Shiki support. Important for MCP, architecture, and AI pattern posts
- **D-06:** Update content collection to support `.mdx` files (change glob pattern or add MDX loader)

### Homepage Writing Section
- **D-07:** Show latest 3 published posts on homepage, sorted by pubDate descending
- **D-08:** "View all writing →" link below the 3 posts, linking to `/writing` index page
- **D-09:** Post cards are both clickable (entire card links to post) AND have an explicit "Read more →" text link at bottom for clarity

### Writing Index Page
- **D-10:** `/writing` page lists all published posts (draft filtered out), sorted by pubDate descending
- **D-11:** Same card design as homepage but shows all posts, not just latest 3

### Schema Updates
- **D-12:** Writing collection glob pattern updated to support `.mdx` files
- **D-13:** Existing schema fields preserved (title, description, pubDate, tags, draft)

### Claude's Discretion
- Post page hero gradient/image approach (reuse hero gradient pattern or simpler approach)
- Writing index page layout (grid vs list)
- Navigation between posts (previous/next links)
- Tag filtering on index page (if useful)
- Prose typography styles for markdown content
- Back navigation from post page

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Existing implementation (modify)
- `src/components/sections/Writing.astro` — Current "Coming soon" section, replace with latest 3 posts
- `src/content.config.ts` — Writing collection schema, update glob for MDX
- `astro.config.mjs` — Add @astrojs/mdx integration
- `src/layouts/Layout.astro` — Base layout, post pages will use this

### Reference implementation
- `~/Workspaces/justblackmagic/justblackmagic-astro/` — BlackMagic site's MDX blog (reference for patterns)

### Existing content
- `src/content/writing/*.md` — 3 draft articles (will become .mdx when published)

### Design system
- `src/styles/global.css` — Theme tokens
- `.planning/phases/01-foundation-scaffolding/01-UI-SPEC.md` — Spacing, typography, color constraints

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- Card, Badge, SectionHeader components — for post cards on homepage/index
- Layout.astro — base layout with nav/footer (post pages wrap in this)
- Writing collection with draft filtering — already works
- Icon component (astro-icon) — for arrow icons on "Read more" links
- Hero gradient pattern from Hero.astro — potential reuse for post page hero

### Established Patterns
- Content collections with Zod schemas and glob loaders
- getCollection() with filter callbacks for draft filtering
- Static output mode — all pages generated at build time
- Tailwind prose classes for markdown content styling

### Integration Points
- `src/pages/writing/[...slug].astro` — dynamic route for individual posts (new)
- `src/pages/writing/index.astro` — writing index page (new)
- Homepage Writing section — update to show latest 3 with links

</code_context>

<specifics>
## Specific Ideas

- Post pages should feel like thought leadership pieces, not casual blog posts
- The immersive full-width hero sets the tone — this is professional writing, not a dev blog
- Code blocks are important for the MCP and architecture posts
- BlackMagic site is the closest reference for MDX blog patterns on Astro + Cloudflare
- Keep the dark theme consistent — prose content needs good contrast for readability

</specifics>

<deferred>
## Deferred Ideas

- RSS feed (REQUIREMENTS V2-01 — already tracked)
- Reading time estimate (explicitly decided against for now)
- Tag-based filtering page
- Related posts at bottom of post page
- Social share buttons on posts

</deferred>

---

*Phase: 05-writing-blog-system*
*Context gathered: 2026-03-27*
