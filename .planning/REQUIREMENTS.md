# Requirements: russellkmoore.me

**Defined:** 2026-03-26
**Core Value:** Visitors immediately understand Russell's expertise and can see tangible proof of what he's built.

## v1 Requirements

Requirements for initial release. Each maps to roadmap phases.

### Infrastructure

- [x] **INFRA-01**: Site builds with Astro 5 + TypeScript and deploys to Cloudflare Workers via wrangler
- [x] **INFRA-02**: Tailwind CSS v4 configured with dark theme tokens (~#0a0a0f bg, #7c3aed violet accent, off-white text, muted gray secondary)
- [x] **INFRA-03**: Geist Sans font loaded via @fontsource with Inter fallback, no visible layout shift
- [ ] **INFRA-04**: Content collections defined for projects/ (title, description, stack[], status, url?, featured) and writing/ (title, date, excerpt, tags[], draft)
- [ ] **INFRA-05**: Seed project entries created: Mercora and RecompAI
- [ ] **INFRA-06**: Seed writing entries created (all draft:true): 3 articles
- [x] **INFRA-07**: npm scripts work: dev, build, deploy
- [ ] **INFRA-08**: README documents local dev setup and deploy instructions

### Layout

- [ ] **LAYOUT-01**: Layout.astro wraps all pages with consistent head, nav, and footer
- [ ] **LAYOUT-02**: Sticky nav with name/logo left, section links right, hamburger menu on mobile
- [ ] **LAYOUT-03**: Minimal footer with copyright + LinkedIn + GitHub icons
- [ ] **LAYOUT-04**: Site is fully responsive across mobile, tablet, and desktop breakpoints

### Components

- [ ] **COMP-01**: Reusable Card.astro component with consistent styling
- [ ] **COMP-02**: Reusable Badge.astro component for tech stack tags and status indicators
- [ ] **COMP-03**: Reusable SectionHeader.astro component for section titles

### Hero

- [ ] **HERO-01**: Hero displays name and tagline (tech leadership angle)
- [ ] **HERO-02**: Two CTA buttons: "See My Work" anchors to Projects, "Let's Talk" anchors to Contact
- [ ] **HERO-03**: Subtle animated gradient background (performant, not flashy)

### About

- [ ] **ABOUT-01**: Short narrative paragraph about Russell
- [ ] **ABOUT-02**: 4 stats cards displaying: 20+ Years, $2B+ Platform Scale, $13M ARR Built & Acquired, 200+ Global Team

### Projects

- [ ] **PROJ-01**: Card grid displaying projects from content collection
- [ ] **PROJ-02**: Each project card shows title, description, tech stack tags (Badge), and status badge (Live / In Development)
- [ ] **PROJ-03**: Featured projects visually distinguished or prioritized in display order

### Career

- [ ] **CAREER-01**: 3 highlight cards for AT&T, Spark::red, and Pivotree
- [ ] **CAREER-02**: Each card shows company name, role context, and key quantified metrics

### Writing

- [ ] **WRITE-01**: Card grid displaying published writing from content collection
- [ ] **WRITE-02**: Graceful "coming soon" state when no published posts exist (all drafts filtered out)
- [ ] **WRITE-03**: Draft entries (draft:true) never render on the site

### Contact

- [ ] **CONTACT-01**: Email link and LinkedIn link displayed
- [ ] **CONTACT-02**: Brief "what I'm open to" copy (full-time roles, consulting, advising, interesting conversations)

### SEO

- [ ] **SEO-01**: Page title, meta description, and canonical URL set
- [ ] **SEO-02**: Open Graph tags (og:title, og:description, og:image) for LinkedIn/Slack link previews
- [ ] **SEO-03**: Semantic HTML with proper heading hierarchy and accessibility basics (alt text, ARIA labels, keyboard nav)

## v2 Requirements

Deferred to future release. Tracked but not in current roadmap.

### Content Enhancements

- **V2-01**: RSS feed for writing posts
- **V2-02**: Reading time estimate on writing entries
- **V2-03**: Project demo embeds or screenshot galleries
- **V2-04**: Individual project detail pages (case studies)
- **V2-05**: Individual writing post pages (when posts are published)

## Out of Scope

Explicitly excluded. Documented to prevent scope creep.

| Feature | Reason |
|---------|--------|
| Contact form | Spam magnet, needs backend -- email link is sufficient |
| CMS / admin panel | Over-engineering for <20 content items -- markdown in repo |
| Dark/light mode toggle | Dark theme IS the brand -- commit to it |
| Blog comments | Requires moderation/backend -- link to LinkedIn discussions |
| Chatbot / AI assistant | Gimmicky on a personal site -- content speaks for itself |
| Heavy animations (GSAP, parallax) | Annoys leadership audience -- subtle CSS transitions only |
| Skill bars / percentage ratings | Meaningless and cringe -- projects demonstrate skill |
| Testimonials section | Hard to avoid looking self-promotional -- LinkedIn handles this |
| Authentication | No gated content needed |
| Mobile app | Web only |

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| INFRA-01 | Phase 1 | Complete |
| INFRA-02 | Phase 1 | Complete |
| INFRA-03 | Phase 1 | Complete |
| INFRA-04 | Phase 1 | Pending |
| INFRA-05 | Phase 1 | Pending |
| INFRA-06 | Phase 1 | Pending |
| INFRA-07 | Phase 1 | Complete |
| INFRA-08 | Phase 1 | Pending |
| LAYOUT-01 | Phase 2 | Pending |
| LAYOUT-02 | Phase 2 | Pending |
| LAYOUT-03 | Phase 2 | Pending |
| LAYOUT-04 | Phase 2 | Pending |
| COMP-01 | Phase 2 | Pending |
| COMP-02 | Phase 2 | Pending |
| COMP-03 | Phase 2 | Pending |
| HERO-01 | Phase 2 | Pending |
| HERO-02 | Phase 2 | Pending |
| HERO-03 | Phase 2 | Pending |
| ABOUT-01 | Phase 2 | Pending |
| ABOUT-02 | Phase 2 | Pending |
| PROJ-01 | Phase 2 | Pending |
| PROJ-02 | Phase 2 | Pending |
| PROJ-03 | Phase 2 | Pending |
| CAREER-01 | Phase 2 | Pending |
| CAREER-02 | Phase 2 | Pending |
| WRITE-01 | Phase 2 | Pending |
| WRITE-02 | Phase 2 | Pending |
| WRITE-03 | Phase 2 | Pending |
| CONTACT-01 | Phase 2 | Pending |
| CONTACT-02 | Phase 2 | Pending |
| SEO-01 | Phase 3 | Pending |
| SEO-02 | Phase 3 | Pending |
| SEO-03 | Phase 3 | Pending |

**Coverage:**
- v1 requirements: 33 total
- Mapped to phases: 33
- Unmapped: 0

---
*Requirements defined: 2026-03-26*
*Last updated: 2026-03-26 after roadmap creation*
