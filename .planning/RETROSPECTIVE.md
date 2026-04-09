# Project Retrospective

*A living document updated after each milestone. Lessons feed forward into future planning.*

## Milestone: v1.0 — MVP

**Shipped:** 2026-04-09
**Phases:** 7 | **Plans:** 18 | **Tasks:** 35

### What Was Built
- Complete portfolio site with 6 sections (hero, about, projects, career, writing, contact)
- MDX-powered blog with individual post pages, syntax highlighting, and writing index
- Project detail pages with case study content for 7 projects
- Career section with horizontal accomplishment timelines for 4 companies
- 6-component MDX library (Callout, Figure, CodeComparison, ScreenshotCarousel, InteractiveScreenshot, LinkCard)
- SEO meta tags, OG previews, accessibility features, Cloudflare Workers deployment

### What Worked
- Started with a tight 3-phase plan (foundation, components, polish) and expanded organically as phases 4-7 were added — scope grew but stayed manageable
- Content collections with Zod schemas caught errors at build time consistently
- Reusing layout patterns (PostLayout → ProjectLayout) kept new page types fast to build
- Barrel export + auto-registration for MDX components was a clean DRY pattern
- Tailwind v4 CSS-first config was simpler than expected — no JS config file needed

### What Was Inefficient
- ROADMAP.md progress table fell out of sync with actual completion (some phases showed "In Progress" when complete)
- REQUIREMENTS.md checkboxes weren't updated as work shipped — 7 items were complete but unchecked at milestone close
- Original 3-phase plan expanded to 7 phases — requirements doc didn't evolve to cover phases 4-7
- OG image was deferred and never revisited

### Patterns Established
- ProjectLayout and PostLayout share the same structure: immersive hero, prose content, prev/next nav
- Career data hardcoded in component (not content collection) — appropriate for small static datasets
- MDX components use vanilla JS with no framework dependencies
- Back links point to homepage anchors (/#projects, /#writing) rather than separate index pages

### Key Lessons
1. Keep REQUIREMENTS.md checkboxes in sync during execution, not just at milestone close — avoids confusion about what's actually done
2. When scope grows beyond original phases, add requirement IDs for new phases to maintain traceability
3. CSS-based tooltip approaches fail with overflow:hidden containers — JS fixed-position is more reliable
4. Build-time metadata fetching (LinkCard OG scraping) is powerful but needs error handling for sites that block bots

---

## Cross-Milestone Trends

### Process Evolution

| Milestone | Phases | Plans | Key Change |
|-----------|--------|-------|------------|
| v1.0 | 7 | 18 | Initial build — expanded from 3 to 7 phases organically |

### Top Lessons (Verified Across Milestones)

1. Keep tracking artifacts (requirements, progress tables) updated during execution, not just at boundaries
