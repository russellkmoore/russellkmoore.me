# Phase 4: Career Timelines - Context

**Gathered:** 2026-03-26
**Status:** Ready for planning

<domain>
## Phase Boundary

Replace the current placeholder Career section with accurate resume-based content and per-company horizontal accomplishment timelines. Fix wrong titles/numbers in existing Career.astro. Research AT&T milestone dates. Build graphical horizontal timeline components with hover-to-expand detail cards. Add Black Magic Consulting as 4th company entry.

</domain>

<decisions>
## Implementation Decisions

### Timeline Visual Design
- **D-01:** Horizontal bar timelines for each company — a horizontal line with year markers and milestones branching off above/below, like the Spark::red 10-year poster
- **D-02:** Mobile responsive behavior: Claude's discretion (horizontal scroll vs collapse to vertical)
- **D-03:** Milestones show short label on timeline, hover/click reveals tooltip or card with full detail

### Content & Accuracy
- **D-04:** Use exact resume titles:
  - AT&T: "Chief Architect, E-Commerce" (1997–2008)
  - Spark::red: "Co-Founder & CEO" (2008–2018)
  - Pivotree: "SVP, Managed Services" (2018–2019)
  - Black Magic Consulting: "Managing Director" (2021–Present)
- **D-05:** Date precision: quarter or month preferred. Research publicly available dates, year is acceptable fallback for anything not findable
- **D-06:** Fix all wrong data in current Career.astro (wrong titles, wrong numbers, wrong descriptions)

### AT&T Milestones to Research
- **D-07:** Research with dates:
  - Launch of attws.com (AT&T Wireless Services)
  - eCare integration into the platform for customer care
  - Amdocs integration for eBill presentment & payments
  - iPhone launch (June 2007 — known)
  - Platform scaling from marketing site to full transactional system
  - PCI and SOX compliance architecture

### Spark::red Milestones (from 10-year poster)
- **D-08:** Source data from resume-content.md — full year-by-year breakdown already captured from poster photo:
  - 2007: Founded
  - 2008: 1st client (People's Choice Awards, 140K pageviews/min)
  - 2009: Furniture Row, PayPal ATG Integration Module
  - 2010: 1st office Redmond
  - 2011: Fortune 1000 clients, PCI Level 1
  - 2012: European expansion, 10 Sparklers
  - 2013: 400%/600% traffic growth
  - 2014: OOW presentation, 20+ team
  - 2015: New Relic Partner, Sur la Table record holiday
  - 2016: NR Platform Partner of Year, Insight Europe, 30+ team
  - 2017+: Truworths, acquisition

### Pivotree Milestones to Research
- **D-09:** Research with dates:
  - Spark::red acquisition/integration into Pivotree
  - Thinkwrap integration into Pivotree
  - Tenzing integration into Pivotree
  - IPO preparation and execution ($70M CAD valuation)

### Black Magic Consulting Milestones
- **D-10:** From resume:
  - 2021: Founded consulting practice
  - Specialty manufacturing digital transformation
  - Q1 2025: RBM Software strategic turnaround
  - AI/ML optimization strategy for e-commerce retailer

### Page Structure
- **D-11:** Expand in-place — keep existing vertical company timeline (now 4 entries). Each company shows title, dates, bullet summary, then its horizontal accomplishment timeline directly below. All visible, no accordions
- **D-12:** Company order: AT&T → Spark::red → Pivotree → Black Magic Consulting (chronological)

### Claude's Discretion
- Exact timeline component CSS/HTML implementation
- Mobile responsive behavior for horizontal timelines
- Tooltip/card design for hover-to-expand milestone details
- How many milestones to show per company (curate from full list vs show all)
- Transition animations for hover states

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Source content
- `.planning/phases/04-career-timelines-with-researched-accomplishments-and-graphical-timeline-components-for-at-t-spark-red-and-pivotree/resume-content.md` — Full resume data and Spark::red poster transcription

### Existing implementation (modify)
- `src/components/sections/Career.astro` — Current career section with WRONG data (must be completely rewritten)
- `src/components/SectionHeader.astro` — Reusable section header component

### Design system
- `src/styles/global.css` — Theme tokens (accent #a78bfa, background #0a0a0f, muted #a1a1aa)
- `.planning/phases/01-foundation-scaffolding/01-UI-SPEC.md` — Spacing, typography, color constraints

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- SectionHeader component — used by Career section
- Card component — can be used for milestone detail cards
- Badge component — can be used for year markers
- Existing vertical timeline CSS pattern (border-l-2, dot positioning) — reference for consistency

### Established Patterns
- Astro components with Props interface
- Tailwind v4 CSS-first config
- No JS frameworks (vanilla JS only for interactions)
- Dark theme with accent color

### Integration Points
- Career section at `#career` in index.astro — already wired
- No content collections for career data — hardcoded in component (per Phase 2 decision)

</code_context>

<specifics>
## Specific Ideas

- The Spark::red poster photo is the visual reference — horizontal bar with milestones branching off, year markers along the line
- Hover/click detail cards should feel like tooltips, not full modals — lightweight, informative
- Current Career.astro data is fabricated/wrong — must be completely replaced with real resume data
- Research phase should find AT&T and Pivotree dates from public sources (press releases, SEC filings, news articles)
- The timeline is a key differentiator — most portfolio sites just list roles. This shows the journey and impact over time

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 04-career-timelines*
*Context gathered: 2026-03-26*
