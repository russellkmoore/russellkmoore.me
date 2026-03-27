# Phase 4: Career Timelines - Research

**Researched:** 2026-03-26
**Domain:** Historical career milestone dates + CSS horizontal timeline components
**Confidence:** MEDIUM (historical dates partially verified, technical patterns well-established)

## Summary

This phase has two dimensions: (1) researching historically accurate dates for career milestones at AT&T Wireless, Spark::red, and Pivotree, and (2) implementing horizontal timeline components using CSS/Tailwind with hover-to-expand detail cards.

Historical research yielded strong results for Pivotree (merger date, rebrand date, IPO date all verified from primary sources) and Spark::red (poster data already captured). AT&T Wireless e-commerce specifics are harder to find publicly -- the attws.com platform was internal-facing architecture work, and specific launch dates for eCare and Amdocs integrations are not in public records. For AT&T, we have firm dates for corporate milestones (IPO, Cingular acquisition, rebrand, iPhone launch) but will need year-level precision for e-commerce platform milestones.

**Primary recommendation:** Use verified public dates where available, fall back to year-level estimates for AT&T platform milestones, and build a reusable TimelineBar.astro component using CSS flexbox with Tailwind `group-hover` tooltips (no JS libraries needed).

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- D-01: Horizontal bar timelines for each company -- horizontal line with year markers and milestones branching off above/below
- D-02: Mobile responsive behavior is Claude's discretion
- D-03: Milestones show short label on timeline, hover/click reveals tooltip/card with full detail
- D-04: Exact resume titles: AT&T "Chief Architect, E-Commerce" (1997-2008), Spark::red "Co-Founder & CEO" (2008-2018), Pivotree "SVP, Managed Services" (2018-2019), Black Magic Consulting "Managing Director" (2021-Present)
- D-05: Date precision: quarter/month preferred, year acceptable fallback
- D-06: Fix all wrong data in current Career.astro
- D-07: Research AT&T milestones with dates (attws.com launch, eCare, Amdocs, iPhone, PCI/SOX)
- D-08: Spark::red milestones sourced from poster data in resume-content.md
- D-09: Research Pivotree milestones (Spark::red acquisition, Thinkwrap, Tenzing, IPO)
- D-10: Black Magic Consulting milestones from resume
- D-11: Expand in-place -- keep vertical company timeline (now 4 entries), each with horizontal accomplishment timeline below. All visible, no accordions
- D-12: Company order: AT&T -> Spark::red -> Pivotree -> Black Magic Consulting (chronological)

### Claude's Discretion
- Exact timeline component CSS/HTML implementation
- Mobile responsive behavior for horizontal timelines
- Tooltip/card design for hover-to-expand milestone details
- How many milestones to show per company (curate from full list vs show all)
- Transition animations for hover states

### Deferred Ideas (OUT OF SCOPE)
None
</user_constraints>

## Researched Historical Dates

### AT&T Wireless (1997-2008) -- Chief Architect, E-Commerce

| Milestone | Date | Confidence | Source |
|-----------|------|------------|--------|
| Russell joins AT&T Wireless | 1997 | HIGH | Resume |
| attws.com active as marketing site | ~1999-2000 | MEDIUM | Wikipedia (AT&T Wireless IPO April 2000 implies web presence); Wayback Machine shows attws.com captures from this era |
| AT&T Wireless IPO (world's largest at time) | April 2000 | HIGH | Wikipedia, encyclopedia.com |
| AT&T Wireless becomes independent company | July 9, 2001 | HIGH | Wikipedia |
| attws.com transactional e-commerce capabilities | ~2001-2002 | LOW | Estimated based on industry timing; no public source found for specific launch |
| Sarbanes-Oxley Act enacted (SOX compliance begins) | July 30, 2002 | HIGH | Wikipedia (PCI DSS), public law |
| eCare customer self-service integration | ~2002-2003 | LOW | No public date found; AT&T Wireless installed Siebel CRM in 2001; eCare likely built on this platform |
| Number portability crisis (FCC mandate) | November 2003 | HIGH | Wikipedia -- created "public relations nightmare" for AT&T Wireless |
| Cingular acquires AT&T Wireless ($41B) | October 26, 2004 | HIGH | Wikipedia, FCC records |
| PCI DSS v1.0 released (PCI compliance framework) | December 2004 | HIGH | PCI SSC history, Wikipedia |
| AT&T Wireless brand retired by Cingular | April 26, 2005 | HIGH | Wikipedia |
| Amdocs eBill integration | ~2003-2005 | LOW | No specific public date found; Amdocs had "decades-long" AT&T relationship; CBIS handled billing through 2001, Amdocs likely took over after |
| Cingular rebrands to AT&T Mobility | January-June 2007 | HIGH | Wikipedia -- announced January 2007, transition completed by late June 2007 |
| iPhone exclusive launch | June 29, 2007 | HIGH | Apple newsroom, Wikipedia |
| $2B+ annual digital revenue milestone | ~2006-2007 | LOW | Resume claim; no public source for specific date. Likely achieved during AT&T Mobility era given iPhone-driven online traffic |

**Key corporate context for timeline:**
- 1997-2004: AT&T Wireless Services (Russell's initial employer)
- 2004-2007: Cingular Wireless (same role, company acquired)
- 2007-2008: AT&T Mobility (Cingular rebranded)
- Russell's 11-year tenure spans all three brand eras

**What could NOT be verified publicly:**
- Exact attws.com e-commerce launch date (internal platform milestone)
- Exact eCare integration date (internal project)
- Exact Amdocs eBill integration date (contract terms not public for wireless division specifically)
- Exact date $2B revenue threshold crossed (internal metric)

**Recommendation:** For unverifiable dates, use year-level markers on the timeline. The user can refine from memory during implementation review. The publicly verified corporate milestones (IPO, acquisition, rebrand, iPhone) provide strong anchor points.

### Spark::red (2007-2018) -- Co-Founder & CEO

All dates sourced from the 10-year poster transcription in resume-content.md. Confidence: HIGH (primary source).

| Year | Milestone(s) |
|------|-------------|
| 2007 | Founded ("Spark::red was born") |
| 2008 | 1st client -- People's Choice Awards (140K pageviews/min sustained) |
| 2009 | 2nd client -- Furniture Row; PayPal ATG Integration Module |
| 2010 | 1st office -- Redmond, WA |
| 2011 | 1st & 2nd Fortune 1000 clients; PCI Level 1 certified |
| 2012 | 1st worldwide redesign; 1st European client; 1st South African client; Doubled clients; 10 Sparklers |
| 2013 | 1st Spark::red Insight event; 400% website traffic growth; 600% mobile traffic growth |
| 2014 | 1st presentation at Oracle OpenWorld (OOW); 20+ Sparklers |
| 2015 | 1st sales person; New Relic Partner; Sur la Table record holiday sales |
| 2016 | 1st Spark::red Insight Europe; 30+ Sparklers; New Relic Platform Partner of the Year |
| 2017 | Truworths engagement; acquisition process begins |

**Clients visible on poster:** Ann Taylor, mrp, Canon, Sur la Table, Quiksilver, TBC, Restoration Hardware, Vitacost, Roxy, rue21, ProFlowers, Gildan, Furniture Row, Maison Martin Margiela, Calendars, People's Choice Awards, Deluxe, Michael Kors, Vera Bradley

### Pivotree (2018-2019) -- SVP, Managed Services

| Milestone | Date | Confidence | Source |
|-----------|------|------------|--------|
| Tenzing, Thinkwrap, and Spark::red merge | February 13, 2018 | HIGH | Newswire press release |
| Merger closes, combined entity operational | February 2018 | HIGH | Newswire press release |
| Pivotree brand name introduced (unification) | March 20, 2019 | HIGH | PRWeb press release, Eventi Capital |
| Pivotree IPO on TSX Venture Exchange (PVT) | October 30, 2020 | HIGH | Pivotree newsroom |
| IPO share price | C$8.50/share | HIGH | Pivotree newsroom |
| IPO gross proceeds | ~C$69M (incl. over-allotment) | HIGH | Private Capital Journal, Globe and Mail |
| Post-IPO market valuation | ~C$201M | HIGH | Private Capital Journal |

**Note on user's "$70M CAD valuation" reference:** The IPO raised ~C$69M in gross proceeds (close to $70M), but the post-IPO market valuation was ~C$201M. The $70M figure likely refers to the proceeds raised, not the company valuation. The planner should use the accurate figures.

**Merger details from press release:**
- Tenzing (Toronto) -- commerce platform deployment and management (Oracle Commerce, SAP Hybris, Intershop, Magento)
- Thinkwrap Commerce (Ottawa, founded 2004) -- strategy, design, development for enterprise e-commerce; SAP Hybris Gold Partner
- Spark::red (Redmond, WA, founded 2007) -- premium managed hosting for Oracle Commerce platforms
- Leadership: Bill Di Nardo (CEO from Tenzing), Russell Moore (Co-founder, Spark::red) became SVP
- Financing: Series D from Eventi Capital Partners, debt from BMO Bank of Montreal

### Black Magic Consulting (2021-Present) -- Managing Director

All from resume. Confidence: HIGH (primary source).

| Milestone | Date | Confidence |
|-----------|------|------------|
| Founded consulting practice | 2021 | HIGH |
| Specialty manufacturing digital transformation | 2021-ongoing | HIGH |
| RBM Software strategic turnaround | Q1 2025 | HIGH |
| AI/ML optimization strategy for e-commerce | 2024-2025 (est.) | MEDIUM |

## Architecture Patterns

### Component Structure

```
src/components/
  sections/
    Career.astro          # Main section (rewrite completely)
  timeline/
    CompanyEntry.astro    # Vertical entry: title, dates, summary + horizontal timeline
    TimelineBar.astro     # Horizontal milestone timeline with hover cards
    MilestoneNode.astro   # Individual milestone dot + tooltip
```

### Pattern 1: Horizontal Timeline Bar

**What:** A horizontal line representing a time span with milestone dots positioned proportionally by year. Milestones branch above/below the line with short labels, and hovering reveals a detail card.

**When to use:** For each company's accomplishment timeline.

**Implementation approach:**
```astro
---
interface Props {
  startYear: number;
  endYear: number;
  milestones: {
    year: number;
    label: string;
    detail: string;
    position: "above" | "below";
  }[];
}
---

<div class="relative w-full overflow-x-auto pb-8 pt-16">
  <div class="relative min-w-[600px] mx-auto">
    <!-- Year markers along bottom -->
    <div class="flex justify-between px-4">
      {/* Year labels */}
    </div>

    <!-- Horizontal line -->
    <div class="absolute left-0 right-0 top-1/2 h-0.5 bg-white/20"></div>

    <!-- Milestone nodes positioned with percentage-based left offsets -->
    {milestones.map((m) => (
      <div
        class="group absolute"
        style={`left: ${((m.year - startYear) / (endYear - startYear)) * 100}%`}
      >
        <!-- Dot -->
        <div class="size-3 rounded-full bg-accent border-2 border-background" />

        <!-- Short label (always visible) -->
        <span class="text-xs text-muted whitespace-nowrap">{m.label}</span>

        <!-- Detail card (hover) -->
        <div class="absolute hidden group-hover:block z-20
                    rounded-lg border border-white/10 bg-background/95
                    backdrop-blur-sm p-3 w-56 text-sm shadow-lg">
          <p class="font-semibold text-accent">{m.year}</p>
          <p class="text-muted mt-1">{m.detail}</p>
        </div>
      </div>
    ))}
  </div>
</div>
```

### Pattern 2: CSS-Only Tooltips with Tailwind `group-hover`

**What:** Tooltip cards that appear on hover using only Tailwind utility classes. No JS required.

**Key classes:**
```
Parent:  group relative
Tooltip: absolute hidden group-hover:block z-20
```

**Positioning logic:**
- Milestones in "above" position: tooltip appears above the line
- Milestones in "below" position: tooltip appears below the line
- Use `bottom-full mb-2` for above, `top-full mt-2` for below

### Pattern 3: Mobile Responsive Timeline

**Recommendation:** Horizontal scroll with scroll-snap on mobile.

```css
/* Container */
.timeline-scroll {
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  -webkit-overflow-scrolling: touch;
}

/* Each milestone acts as snap point */
.timeline-scroll > * {
  scroll-snap-align: center;
}
```

On screens below `md` breakpoint:
- Set `min-width: 600px` on the inner timeline to force horizontal scrolling
- Add a subtle "scroll to explore" indicator with a left/right arrow
- Alternatively, collapse to a compact vertical list (simpler, less visual impact)

**Recommended approach:** Horizontal scroll with snap. It preserves the visual metaphor and works well on touch devices. The timeline is a key differentiator for this portfolio -- collapsing to vertical on mobile loses that impact.

### Anti-Patterns to Avoid
- **Fixed-width milestone spacing:** Milestones should be positioned proportionally by year, not evenly spaced. AT&T spans 11 years with milestones clustered in certain periods.
- **Accordion hiding:** D-11 explicitly says all visible, no accordions.
- **JS-heavy timeline libraries:** No JS frameworks allowed (CLAUDE.md). Pure CSS + minimal vanilla JS if needed for mobile touch.
- **Modal popups for details:** D-03 says tooltips, not modals. Keep it lightweight.

## Existing Career.astro -- What's Wrong

The current Career.astro has fabricated data that MUST be completely replaced:

| Current (WRONG) | Correct |
|-----------------|---------|
| "Sr. Director, Engineering" | "Chief Architect, E-Commerce" |
| "Managed 200+ engineers" | 200+ is the Pivotree number, not AT&T |
| "monolith to composable microservices" | Not accurate for the era (2000s) |
| "Co-founder and CTO" | "Co-Founder & CEO" |
| "AI-powered product" | E-commerce managed services, not AI |
| "$3M ARR before exit" | Not stated in resume; was acquisition by Pivotree |
| "VP, Engineering and Technology" | "SVP, Managed Services" |
| "$13M in ARR through organic growth and M&A" | $30M managed services division |
| Missing: Black Magic Consulting | Must add as 4th entry |
| 3 companies shown | 4 companies required |

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Tooltip positioning | Custom JS tooltip library | Tailwind `group-hover` + `absolute` positioning | Pure CSS, no bundle cost, works in static Astro |
| Proportional positioning | Manual pixel calculations | CSS `calc()` with percentage-based `left` offset | Responsive, adapts to container width |
| Horizontal scroll on mobile | Custom touch handlers | `overflow-x: auto` + `scroll-snap-type` | Native browser behavior, smooth on all devices |
| Icon for scroll hint | Custom SVG | `astro-icon` with Lucide arrow icons | Already in project stack |

## Common Pitfalls

### Pitfall 1: Tooltip Clipping at Container Edges
**What goes wrong:** Tooltips on the first and last milestones get clipped by `overflow-x: auto` on the scroll container.
**Why it happens:** `overflow: auto/hidden` creates a new stacking context that clips positioned children.
**How to avoid:** Either (a) add padding to the scroll container so tooltips have room, or (b) position edge tooltips to open inward (left-aligned for first, right-aligned for last).
**Warning signs:** Test hover on the first and last milestones in every timeline.

### Pitfall 2: Milestone Overlap on Dense Timelines
**What goes wrong:** Multiple milestones in the same year overlap, making labels unreadable.
**Why it happens:** Percentage-based positioning puts same-year milestones at identical x positions.
**How to avoid:** For same-year milestones, alternate above/below positioning, or group them into a single node with multiple detail lines. Spark::red 2012 has 5+ milestones -- these should be grouped.
**Warning signs:** Spark::red 2011-2012 and 2015-2016 have dense milestone clusters.

### Pitfall 3: Touch vs Hover on Mobile
**What goes wrong:** `hover` tooltips don't work on touch devices -- users can't see milestone details.
**Why it happens:** No hover state on touch screens.
**How to avoid:** Add `focus` as an alternative trigger (make milestone nodes focusable with `tabindex="0"`), or use `click` via minimal vanilla JS to toggle tooltip visibility on mobile.
**Warning signs:** Test on actual mobile device or Chrome DevTools touch simulation.

### Pitfall 4: Hardcoded Wrong Data
**What goes wrong:** Copying patterns from current Career.astro perpetuates wrong titles and numbers.
**Why it happens:** Muscle memory or copy-paste from existing code.
**How to avoid:** Delete current Career.astro content entirely and rebuild from resume-content.md and this research document as sole data sources.
**Warning signs:** Any title that doesn't match D-04 exactly.

## Code Examples

### Company Entry with Horizontal Timeline
```astro
<!-- One company block in the vertical timeline -->
<div class="relative">
  <!-- Vertical timeline dot -->
  <div class="absolute -left-[calc(2rem+9px)] top-1 size-4 rounded-full
              border-2 border-accent bg-background"></div>

  <!-- Company info -->
  <div class="flex items-baseline justify-between">
    <h3 class="text-lg font-semibold text-foreground">Spark::red</h3>
    <span class="text-sm text-muted">2008 - 2018</span>
  </div>
  <p class="text-sm text-accent">Co-Founder & CEO</p>
  <p class="mt-2 text-sm text-muted">
    Built from zero to acquisition. Oracle Commerce managed hosting
    for Fortune 1000 brands across 4 continents.
  </p>

  <!-- Horizontal accomplishment timeline -->
  <div class="mt-6">
    <TimelineBar
      startYear={2007}
      endYear={2018}
      milestones={sparkredMilestones}
    />
  </div>
</div>
```

### CSS-Only Tooltip
```html
<div class="group relative cursor-pointer" tabindex="0">
  <!-- Dot -->
  <div class="size-3 rounded-full bg-accent transition-transform
              group-hover:scale-150 group-focus:scale-150"></div>

  <!-- Label (always visible) -->
  <span class="absolute top-full mt-1 left-1/2 -translate-x-1/2
               text-[10px] text-muted whitespace-nowrap">
    iPhone
  </span>

  <!-- Detail card (hover/focus) -->
  <div class="pointer-events-none absolute bottom-full mb-4 left-1/2
              -translate-x-1/2 w-56 rounded-lg border border-white/10
              bg-background/95 p-3 text-sm shadow-xl backdrop-blur-sm
              opacity-0 transition-opacity duration-200
              group-hover:pointer-events-auto group-hover:opacity-100
              group-focus:pointer-events-auto group-focus:opacity-100">
    <p class="font-semibold text-accent">June 2007</p>
    <p class="text-foreground">iPhone Exclusive Launch</p>
    <p class="mt-1 text-muted">
      Maintained 99.9% uptime during 10x traffic spikes
      as exclusive carrier partner.
    </p>
  </div>
</div>
```

### Mobile Scroll Container
```html
<div class="relative overflow-x-auto scroll-smooth
            [-webkit-overflow-scrolling:touch]
            [scroll-snap-type:x_mandatory]">
  <div class="min-w-[600px] relative py-12 px-8">
    <!-- Timeline content -->
  </div>
</div>

<!-- Scroll indicator (shown on mobile only) -->
<p class="mt-2 text-center text-xs text-muted md:hidden">
  Swipe to explore timeline
</p>
```

## Project Constraints (from CLAUDE.md)

- **Tech stack:** Astro + TypeScript + Tailwind CSS v4 + @astrojs/cloudflare (static output)
- **No JS frameworks:** Vanilla JS only for interactions (no React/Vue/Svelte)
- **No heavy animations:** Subtle CSS transitions only (REQUIREMENTS.md out of scope)
- **Tailwind v4:** CSS-first config, use `@theme` tokens, no tailwind.config.js
- **Font:** Geist Sans variable via @fontsource
- **Dark theme:** background #0a0a0f, accent #a78bfa, muted #a1a1aa, foreground #f5f5f5
- **Existing components:** Card.astro, Badge.astro, SectionHeader.astro all available for reuse
- **Career data hardcoded:** No content collection for career entries (Phase 2 decision)

## Sources

### Primary (HIGH confidence)
- [Newswire: Tenzing, Thinkwrap, and Spark::red merge](https://www.newswire.ca/news-releases/tenzing-thinkwrap-and-sparkred-merge-to-create-leading-commerce-services-provider-673903753.html) -- Feb 13, 2018 merger date, company details
- [Pivotree IPO completion newsroom](https://www.pivotree.com/newsroom/pivotree-inc-completes-initial-public-offering/) -- Oct 30, 2020, C$8.50/share, C$60M proceeds
- [Private Capital Journal: Pivotree $60M IPO](https://privatecapitaljournal.com/eventi-capital-beedie-investments-backed-pivotree-prices-60m-tsx-v-ipo-at-201m-valuation/) -- C$201M valuation
- [Globe and Mail: Pivotree IPO upsized](https://www.theglobeandmail.com/business/article-hot-tech-market-leads-to-upsized-ipo-for-toronto-e-commerce-company/) -- ~C$69M with over-allotment
- [PRWeb: Pivotree unites under one banner](https://www.prweb.com/releases/pivotree_unites_key_ecommerce_practices_globally_under_one_banner/prweb16177518.htm) -- March 20, 2019 rebrand
- [Wikipedia: AT&T Wireless Services](https://en.wikipedia.org/wiki/AT&T_Wireless_Services) -- Corporate milestones 1997-2005
- [Wikipedia: AT&T Mobility](https://en.wikipedia.org/wiki/AT&T_Mobility) -- Cingular rebrand and iPhone launch dates
- [Apple newsroom: AT&T iPhone plans](https://www.apple.com/newsroom/2007/06/26AT-T-and-Apple-Announce-Simple-Affordable-Service-Plans-for-iPhone/) -- June 2007 launch confirmation
- [Wikipedia: PCI DSS](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) -- December 2004 v1.0 release
- [Wikipedia: Sarbanes-Oxley Act](https://en.wikipedia.org/wiki/Sarbanes%E2%80%93Oxley_Act) -- July 30, 2002 enactment

### Secondary (MEDIUM confidence)
- [Encyclopedia.com: AT&T Wireless Services](https://www.encyclopedia.com/books/politics-and-business-magazines/att-wireless-services-inc) -- Revenue and operational context
- [TechCrunch: AT&T iPhone exclusivity deal](https://techcrunch.com/2010/05/10/apple-att-iphone-agreement/) -- Five-year exclusive deal context
- [Engadget: Apple/AT&T five-year deal](https://www.engadget.com/2010-05-10-confirmed-apple-and-atandt-signed-five-year-iphone-exclusivity-de.html) -- Deal confirmation

### Tertiary (LOW confidence)
- AT&T Wireless e-commerce platform dates (attws.com launch, eCare, Amdocs) -- no public sources found; dates estimated from corporate context
- $2B annual digital revenue timing -- resume claim, no public verification of specific year

## Metadata

**Confidence breakdown:**
- Historical dates (Pivotree): HIGH -- press releases, IPO filings, multiple cross-verified sources
- Historical dates (Spark::red): HIGH -- primary source (poster transcription from user)
- Historical dates (AT&T corporate): HIGH -- Wikipedia, encyclopedia.com, FCC records
- Historical dates (AT&T e-commerce specifics): LOW -- internal platform milestones not publicly documented
- Technical implementation: HIGH -- well-established CSS patterns, Tailwind group-hover is standard
- Architecture: HIGH -- follows existing Astro component patterns in codebase

**Research date:** 2026-03-26
**Valid until:** Indefinite (historical dates don't change; CSS patterns stable)
