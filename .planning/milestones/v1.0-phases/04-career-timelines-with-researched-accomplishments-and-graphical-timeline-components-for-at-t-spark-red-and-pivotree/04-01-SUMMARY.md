---
phase: 04-career-timelines-with-researched-accomplishments-and-graphical-timeline-components-for-at-t-spark-red-and-pivotree
plan: 01
subsystem: ui
tags: [astro, timeline, components, tailwind, accessibility]

requires:
  - phase: 01-foundation-scaffolding
    provides: Global CSS theme tokens (--color-accent, --color-muted, --color-background)
provides:
  - TimelineBar.astro - horizontal timeline bar with year markers and milestone positioning
  - MilestoneNode.astro - milestone dot with label and accessible hover/focus tooltip
affects: [04-02, 04-03, 04-04, career-section]

tech-stack:
  added: []
  patterns: [percentage-based timeline positioning, milestone clustering with alternating above/below, edge-aware tooltip alignment]

key-files:
  created:
    - src/components/timeline/MilestoneNode.astro
    - src/components/timeline/TimelineBar.astro
  modified: []

key-decisions:
  - "Milestone positioning uses same coordinate space as year markers (absolute left-8 right-8 container)"
  - "Milestone clustering auto-alternates above/below for same-year entries regardless of input position"
  - "Year markers use step logic: every year for spans <=8, every 2 years for longer spans"

patterns-established:
  - "Timeline components: percentage-based left positioning within padded absolute container"
  - "Accessible tooltips: group-hover + group-focus with tabindex=0 for keyboard/touch"
  - "Edge-aware tooltips: left-0 or right-0 alignment to prevent container clipping"

requirements-completed: []

duration: 2min
completed: 2026-03-27
---

# Phase 04 Plan 01: Timeline Components Summary

**Reusable MilestoneNode and TimelineBar Astro components with proportional positioning, accessible tooltips, and mobile horizontal scroll**

## Performance

- **Duration:** 2 min
- **Started:** 2026-03-27T06:04:42Z
- **Completed:** 2026-03-27T06:06:39Z
- **Tasks:** 2
- **Files modified:** 2

## Accomplishments
- MilestoneNode with keyboard/touch-accessible hover/focus tooltip and edge-aware positioning
- TimelineBar with percentage-based milestone layout, year markers with step logic, and mobile scroll
- Milestone clustering logic auto-alternates above/below for same-year milestones

## Task Commits

Each task was committed atomically:

1. **Task 1: Create MilestoneNode.astro** - `61409c9` (feat)
2. **Task 2: Create TimelineBar.astro** - `efed9ba` (feat)

## Files Created/Modified
- `src/components/timeline/MilestoneNode.astro` - Milestone dot with label, hover/focus tooltip, edge-aware alignment
- `src/components/timeline/TimelineBar.astro` - Horizontal timeline bar with year markers, proportional positioning, mobile scroll

## Decisions Made
- Milestone nodes positioned within same absolute left-8 right-8 container as year markers for consistent alignment
- Milestone clustering overrides input position to auto-alternate above/below, preventing label overlap
- Year marker step logic: every year for spans <=8 years, every 2nd year for longer spans

## Deviations from Plan

### Auto-fixed Issues

**1. [Rule 1 - Bug] Fixed milestone coordinate space mismatch**
- **Found during:** Task 2 (TimelineBar creation)
- **Issue:** Initial implementation positioned milestones using calc() relative to the outer container, but year markers use percentage-based positioning within an absolute left-8 right-8 inner container, causing misalignment
- **Fix:** Moved milestones into the same absolute left-8 right-8 container as year markers, using matching percentage-based left positioning
- **Files modified:** src/components/timeline/TimelineBar.astro
- **Verification:** Build passes, positioning logic matches year markers
- **Committed in:** efed9ba (Task 2 commit)

---

**Total deviations:** 1 auto-fixed (1 bug)
**Impact on plan:** Essential for correct visual alignment. No scope creep.

## Issues Encountered
None

## User Setup Required
None - no external service configuration required.

## Next Phase Readiness
- Timeline components ready to receive milestone data arrays from per-company career pages
- Plans 02-04 can import TimelineBar and pass company-specific milestone data
- No blockers

---
*Phase: 04-career-timelines*
*Completed: 2026-03-27*
