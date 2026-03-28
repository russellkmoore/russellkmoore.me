# Roadmap: russellkmoore.me

## Overview

Three phases deliver Russell's portfolio site from empty repo to production. Phase 1 establishes the Astro/Tailwind/Cloudflare foundation with correct configuration (resolving all six critical pitfalls identified in research). Phase 2 builds every visual component and section with real content, producing a complete single-page portfolio. Phase 3 hardens for production with SEO, performance optimization, and deployment verification.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Foundation & Scaffolding** - Astro project with Tailwind v4, content collections, dark theme tokens, and verified Cloudflare deployment
- [ ] **Phase 2: Components & Content** - All sections, reusable components, real content, responsive layout -- complete single-page portfolio
- [x] **Phase 3: Polish & Production** - SEO meta tags, Lighthouse optimization, OG previews, and production verification (completed 2026-03-27)

## Phase Details

### Phase 1: Foundation & Scaffolding
**Goal**: A working Astro project that builds, deploys to Cloudflare, renders a dark-themed page with correct fonts, and serves content from validated collections
**Depends on**: Nothing (first phase)
**Requirements**: INFRA-01, INFRA-02, INFRA-03, INFRA-04, INFRA-05, INFRA-06, INFRA-07, INFRA-08
**Success Criteria** (what must be TRUE):
  1. `npm run dev` starts a local server showing a dark-themed page with Geist Sans font and no layout shift
  2. `npm run build` succeeds and `npm run deploy` pushes to Cloudflare Workers -- the live URL returns the page
  3. Content collections return seed project and writing entries via `getCollection()` with correct typed frontmatter
  4. No white flash on page load (FOUC prevention verified on throttled connection)
  5. README documents local dev setup and deploy commands
**Plans**: 2 plans

Plans:
- [x] 01-01-PLAN.md -- Scaffold Astro 5 project with Tailwind v4, dark theme, fonts, and Cloudflare deploy config
- [x] 01-02-PLAN.md -- Content collection schemas, seed data, proof-of-concept index page, and README

### Phase 2: Components & Content
**Goal**: Visitors see a complete, responsive single-page portfolio with hero, about, projects, career, writing, and contact sections -- all with real content
**Depends on**: Phase 1
**Requirements**: LAYOUT-01, LAYOUT-02, LAYOUT-03, LAYOUT-04, COMP-01, COMP-02, COMP-03, HERO-01, HERO-02, HERO-03, ABOUT-01, ABOUT-02, PROJ-01, PROJ-02, PROJ-03, CAREER-01, CAREER-02, WRITE-01, WRITE-02, WRITE-03, CONTACT-01, CONTACT-02
**Success Criteria** (what must be TRUE):
  1. Visitor sees hero with name, tagline, animated gradient, and two working CTA buttons that scroll to Projects and Contact sections
  2. Visitor can scroll through all six sections (Hero, About, Projects, Career, Writing, Contact) in a coherent single-page flow with sticky navigation
  3. Projects section displays Mercora and RecompAI cards with tech stack tags, status badges, and descriptions from content collections
  4. Writing section shows graceful "coming soon" state (all seed entries are draft:true and never render)
  5. Site is fully usable on mobile with hamburger menu, proper tap targets, and no horizontal overflow
**Plans**: 4 plans
**UI hint**: yes

Plans:
- [x] 02-01-PLAN.md -- Install astro-icon, create reusable components (Card, Badge, SectionHeader), build Nav with mobile menu, Footer, and update Layout
- [x] 02-02-PLAN.md -- Hero section with mesh gradient and CTAs, About section with narrative and stats, Contact section with links
- [x] 02-03-PLAN.md -- Projects section from content collections, Career timeline, Writing section with coming-soon state
- [x] 02-04-PLAN.md -- Compose index.astro with all sections and visual verification checkpoint

### Phase 3: Polish & Production
**Goal**: Site scores 95+ on Lighthouse, displays correct previews when shared on LinkedIn/Slack, and passes a production readiness checklist
**Depends on**: Phase 2
**Requirements**: SEO-01, SEO-02, SEO-03
**Success Criteria** (what must be TRUE):
  1. Page has correct title, meta description, and canonical URL visible in browser tab and page source
  2. Sharing the URL on LinkedIn/Slack shows a rich preview with og:title, og:description, and og:image
  3. HTML uses semantic elements with proper heading hierarchy, alt text on images, ARIA labels, and keyboard navigability
  4. Lighthouse scores 95+ on Performance, Accessibility, Best Practices, and SEO
**Plans**: 2 plans

Plans:
- [x] 03-01-PLAN.md -- SEO meta tags (canonical, OG, Twitter Card), robots.txt, and hero animation speedup
- [x] 03-02-PLAN.md -- Accessibility polish (skip link, focus styles, keyboard nav), OG image, and Lighthouse verification

## Progress

**Execution Order:**
Phases execute in numeric order: 1 -> 2 -> 3 -> 4 -> 5 -> 6

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Foundation & Scaffolding | 2/2 | Complete | 2026-03-27 |
| 2. Components & Content | 2/4 | In Progress|  |
| 3. Polish & Production | 2/2 | Complete   | 2026-03-27 |
| 4. Career Timelines | 2/2 | Complete   | 2026-03-27 |
| 5. Writing/Blog System | 2/3 | In Progress|  |

### Phase 4: Career timelines with researched accomplishments and graphical timeline components for AT&T, Spark::red, and Pivotree

**Goal:** Replace placeholder Career section with accurate resume-based content and per-company horizontal accomplishment timelines for AT&T, Spark::red, Pivotree, and Black Magic Consulting
**Requirements**: None (added phase, no mapped requirement IDs)
**Depends on:** Phase 3
**Plans:** 2/2 plans complete

Plans:
- [x] 04-01-PLAN.md -- Create reusable TimelineBar and MilestoneNode components for horizontal accomplishment timelines
- [x] 04-02-PLAN.md -- Rewrite Career.astro with correct resume data for 4 companies and wire in timeline components

### Phase 5: Writing/blog system with individual post pages, writing index page, and homepage latest posts section

**Goal:** Full MDX-powered blog with individual post pages at /writing/[slug], a /writing index page, and homepage section showing latest 3 posts -- all with syntax-highlighted code blocks and dark-themed prose typography
**Requirements**: None (added phase, no mapped requirement IDs)
**Depends on:** Phase 4
**Plans:** 2/3 plans executed

Plans:
- [x] 05-01-PLAN.md -- Install MDX + typography, configure Shiki syntax highlighting, create PostLayout and PostCard components
- [ ] 05-02-PLAN.md -- Create dynamic post route at /writing/[slug] and publish one test post
- [x] 05-03-PLAN.md -- Create /writing index page and update homepage Writing section with latest 3 posts

### Phase 6: Project detail pages

**Goal:** Individual project detail pages at /projects/[slug] with case study MDX content, tech stack breakdowns, and challenges/outcomes -- homepage project cards link to detail pages instead of expanding on hover
**Requirements**: None (added phase, no mapped requirement IDs)
**Depends on:** Phase 5
**Plans:** 2/2 plans complete

Plans:
- [x] 06-01-PLAN.md -- Update project schema for MDX + images, create ProjectLayout, dynamic route, and update homepage cards with detail links
- [x] 06-02-PLAN.md -- Convert all 7 project .md files to .mdx with case study content

### Phase 7: MDX component library — ScreenshotCarousel, InteractiveScreenshot, Callout, CodeComparison, Figure, and LinkCard for use in writing and project pages

**Goal:** [To be planned]
**Requirements**: TBD
**Depends on:** Phase 6
**Plans:** 0 plans

Plans:
- [ ] TBD (run /gsd:plan-phase 7 to break down)
