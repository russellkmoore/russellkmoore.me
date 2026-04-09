---
phase: 04-career-timelines-with-researched-accomplishments-and-graphical-timeline-components-for-at-t-spark-red-and-pivotree
plan: 02
subsystem: ui
tags: [astro, career, timeline, resume-data, tailwind]

requires:
  - phase: 04-career-timelines (plan 01)
    provides: TimelineBar and MilestoneNode components for horizontal accomplishment timelines
provides:
  - Complete career section with 4 companies, accurate resume data, and per-company horizontal accomplishment timelines
  - Fixed tooltip system (JS-based fixed positioning replacing CSS-only approach)
  - Expanded company descriptions with full resume content
  - Updated project content files with GitHub links and hover-to-expand cards
affects: [phase-05-writing, any future career/content updates]

tech-stack:
  added: []
  patterns:
    - "Fixed-position JS tooltips for timeline milestones (replaced CSS hover)"
    - "Hover-to-expand body content on project cards"
    - "GitHub icon links for public repo projects"

key-files:
  created: []
  modified:
    - src/components/sections/Career.astro
    - src/components/timeline/TimelineBar.astro
    - src/components/timeline/MilestoneNode.astro
    - src/components/sections/Projects.astro
    - src/content.config.ts
    - src/content/projects/akeneo-cloudflare.md
    - src/content/projects/invest-collective.md
    - src/content/projects/mercora.md
    - src/content/projects/quintessential-concierge.md
    - src/content/projects/recompai.md

key-decisions:
  - "Switched from CSS hover tooltips to JS fixed-position tooltips for better overflow handling"
  - "Added Cloudflare Speaker 2015 milestone to Spark::red timeline"
  - "Fixed BMC dates: AI/ML moved to Q3 2025, RBM stays Q1 2025"
  - "Added B2B Greenfield 2023 milestone to BMC timeline"
  - "Expanded all 4 company overviews with full resume descriptions"
  - "Added GitHub icon links for public repos on project cards"
  - "Added hover-to-expand body content on project cards"

patterns-established:
  - "Career data hardcoded in Career.astro frontmatter as milestone arrays"
  - "JS-based tooltip positioning for timeline components"

requirements-completed: []

duration: 15min
completed: 2026-03-27
---

# Phase 4 Plan 02: Career Data Rewrite Summary

**Complete career section with 4 companies (AT&T, Spark::red, Pivotree, Black Magic Consulting), accurate resume titles/dates, horizontal accomplishment timelines with JS tooltips, and updated project content with GitHub links**

## Performance

- **Duration:** ~15 min (includes checkpoint review and post-checkpoint refinements)
- **Started:** 2026-03-26T23:09:00Z
- **Completed:** 2026-03-27T04:45:04Z
- **Tasks:** 2 (1 auto + 1 checkpoint with post-approval refinements)
- **Files modified:** 11

## Accomplishments

- Replaced all fabricated Career.astro data with verified resume content for 4 companies in chronological order
- Wired TimelineBar components with researched milestone dates per company (AT&T 7 milestones, Spark::red 10 milestones, Pivotree 3 milestones, BMC 4 milestones)
- Fixed tooltip system: switched from CSS-only hover to JS fixed-position tooltips for reliable overflow handling
- Updated project content files from workspace .planning data (7 projects total) with GitHub icon links
- Added hover-to-expand body content on project cards

## Task Commits

Each task was committed atomically:

1. **Task 1: Rewrite Career.astro with correct data and timeline components** - `27b9ccc` (feat)
2. **Task 2: Visual verification + post-checkpoint refinements** - `92c7eb1` (feat)

Intermediate commits during checkpoint review:
- `39ea169` - content: update project entries from workspace .planning data
- `6c0468d` - content: add Akeneo Cloudflare, Black Magic, and Quintessential Concierge projects
- `d755906` - merge: plan 04-02 worktree

## Files Created/Modified

- `src/components/sections/Career.astro` - Complete rewrite with 4 companies, correct titles, and horizontal timelines
- `src/components/timeline/TimelineBar.astro` - Refactored tooltip system to JS fixed-position
- `src/components/timeline/MilestoneNode.astro` - Simplified after tooltip refactor
- `src/components/sections/Projects.astro` - Added GitHub icon links and hover-to-expand cards
- `src/content.config.ts` - Added github field to project schema
- `src/content/projects/akeneo-cloudflare.md` - Updated content from .planning data
- `src/content/projects/invest-collective.md` - Updated content from .planning data
- `src/content/projects/mercora.md` - Added GitHub link
- `src/content/projects/quintessential-concierge.md` - Updated content from .planning data
- `src/content/projects/recompai.md` - Added GitHub link
- `src/assets/headshot.jpeg` - Added headshot asset

## Decisions Made

- Switched from CSS hover tooltips to JS fixed-position tooltips -- CSS approach had overflow clipping issues with the timeline container
- Added Cloudflare Speaker 2015 milestone to Spark::red timeline during checkpoint review
- Fixed BMC dates: AI/ML Strategy moved to Q3 2025, RBM Software stays Q1 2025 per user correction
- Added B2B Greenfield 2023 milestone to Black Magic Consulting per user feedback
- Expanded all 4 company overview descriptions with full resume content during checkpoint
- Added GitHub icon links for public repos and hover-to-expand body on project cards as checkpoint enhancements

## Deviations from Plan

### Auto-fixed Issues

**1. [Rule 1 - Bug] Fixed tooltip overflow clipping**
- **Found during:** Checkpoint review
- **Issue:** CSS-only tooltips were clipped by timeline container overflow
- **Fix:** Switched to JS-based fixed-position tooltips that render outside container bounds
- **Files modified:** src/components/timeline/TimelineBar.astro, src/components/timeline/MilestoneNode.astro
- **Committed in:** 92c7eb1

**2. [Rule 2 - Enhancement] Added missing milestones per user feedback**
- **Found during:** Checkpoint review
- **Issue:** Spark::red missing Cloudflare Speaker 2015; BMC missing B2B Greenfield 2023; BMC AI/ML had wrong date
- **Fix:** Added milestones and corrected dates
- **Files modified:** src/components/sections/Career.astro
- **Committed in:** 92c7eb1

**3. [Rule 2 - Enhancement] Expanded company descriptions and project content**
- **Found during:** Checkpoint review
- **Issue:** Company overviews were brief summaries; project content was outdated
- **Fix:** Expanded all 4 company descriptions with full resume content; updated 7 project files from .planning data; added GitHub links
- **Files modified:** src/components/sections/Career.astro, src/components/sections/Projects.astro, src/content.config.ts, 5 project content files
- **Committed in:** 39ea169, 6c0468d, 92c7eb1

---

**Total deviations:** 3 auto-fixed (1 bug fix, 2 enhancements from user checkpoint feedback)
**Impact on plan:** All changes improve accuracy and visual quality. No scope creep -- all within career/project content scope.

## Issues Encountered

None -- build succeeded throughout. Checkpoint feedback was incorporated cleanly.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

- Career section complete with accurate data and interactive timelines
- Project cards enhanced with GitHub links and expandable content
- Phase 5 (Writing/blog system) can proceed -- all content sections are now production-quality
- No blockers or concerns

---
*Phase: 04-career-timelines*
*Completed: 2026-03-27*

## Self-Check: PASSED

- FOUND: src/components/sections/Career.astro
- FOUND: src/components/timeline/TimelineBar.astro
- FOUND: commit 27b9ccc (Task 1)
- FOUND: commit 92c7eb1 (Task 2 + checkpoint refinements)
