# Milestones

## v1.0 MVP (Shipped: 2026-04-09)

**Phases completed:** 7 phases, 18 plans, 35 tasks

**Key accomplishments:**

- Astro 5 static site with Tailwind v4 dark theme, Geist Sans fonts, FOUC prevention, and Cloudflare Workers deploy config
- Astro content collections with glob loaders, Zod schemas for projects/writing, seed entries, and proof-of-concept index page rendering collections end-to-end
- Reusable Card/Badge/SectionHeader components, sticky nav with IntersectionObserver scroll detection and mobile hamburger menu, footer with lucide social icons, Layout wrapping all pages
- Hero with animated mesh gradient and impact tagline, About with first-person narrative and 4 stats cards, Contact with email/LinkedIn CTAs
- Projects/Career/Writing sections with featured card grid, vertical timeline, and draft-filtered coming-soon state
- Complete index.astro composing all 6 section components with gradient dividers into a cohesive single-page portfolio
- Canonical URL, Open Graph tags, Twitter Card tags in Layout head plus robots.txt and faster hero gradient animation (10s with increased opacity)
- Skip-to-content link, focus-visible rings, keyboard-accessible mobile menu with aria-expanded, plus hero gradient and transition visual fixes
- Reusable MilestoneNode and TimelineBar Astro components with proportional positioning, accessible tooltips, and mobile horizontal scroll
- Complete career section with 4 companies (AT&T, Spark::red, Pivotree, Black Magic Consulting), accurate resume titles/dates, horizontal accomplishment timelines with JS tooltips, and updated project content with GitHub links
- MDX pipeline with Shiki syntax highlighting, Tailwind typography prose styles, and reusable PostLayout/PostCard components
- Dynamic route at /writing/[slug] rendering MDX content with PostLayout, prev/next navigation, and one published test post with syntax-highlighted code blocks
- Writing index page at /writing with date-sorted post list, and homepage section showing latest 3 posts with "View all writing" link
- ProjectLayout with immersive hero, dynamic /projects/[slug] route generating 7 pages, and homepage cards linked to detail pages
- Converted 7 project .md files to .mdx with case study content -- Mercora and RecompAI get full 85+ line case studies with tech stack breakdowns, challenges, and outcomes; 5 remaining projects get focused detail pages
- Callout (4 variants with icons), Figure (semantic figcaption), and CodeComparison (side-by-side Shiki blocks) with barrel export
- ScreenshotCarousel with CSS scroll-snap + dialog lightbox and InteractiveScreenshot with pulsing numbered hotspots and fixed-position tooltips
- LinkCard with build-time OG metadata fetching, complete 6-component barrel export, and auto-registration in project/writing page templates

---
