---
phase: 05-writing-blog-system
plan: 03
subsystem: ui
tags: [astro, content-collections, blog, writing, postcards]

requires:
  - phase: 05-01
    provides: PostCard component, writing content collection schema, MDX integration
provides:
  - /writing index page listing all published posts
  - Updated homepage Writing section with latest 3 posts and "View all writing" link
affects: []

tech-stack:
  added: []
  patterns:
    - "getCollection with draft filter and date sort for writing pages"
    - "PostCard component reuse across index and homepage"

key-files:
  created:
    - src/pages/writing/index.astro
  modified:
    - src/components/sections/Writing.astro

key-decisions:
  - "Vertical list layout for /writing index (thought leadership feel vs grid)"
  - "3-column grid for homepage writing section (compact preview)"

patterns-established:
  - "Writing index uses single-column list; homepage uses 3-col grid for latest posts"

requirements-completed: []

duration: 2min
completed: 2026-03-27
---

# Phase 05 Plan 03: Writing Index Page and Homepage Writing Section Summary

**Writing index page at /writing with date-sorted post list, and homepage section showing latest 3 posts with "View all writing" link**

## Performance

- **Duration:** 2 min
- **Started:** 2026-03-27T12:21:36Z
- **Completed:** 2026-03-27T12:23:45Z
- **Tasks:** 2
- **Files modified:** 2

## Accomplishments
- Created /writing index page listing all published posts sorted by date descending
- Updated homepage Writing section to show latest 3 posts as PostCards in a 3-column grid
- Added "View all writing" navigation link from homepage to /writing
- Both pages use PostCard component for consistent rendering
- Empty state fallback ("Coming soon") preserved on both pages

## Task Commits

Each task was committed atomically:

1. **Task 1: Create /writing index page** - `72590fb` (feat)
2. **Task 2: Update homepage Writing section with latest 3 posts** - `21dbe7c` (feat)

## Files Created/Modified
- `src/pages/writing/index.astro` - Writing index page with getCollection, date sort, PostCard rendering
- `src/components/sections/Writing.astro` - Updated to use PostCard, slice(0,3), "View all writing" link

## Decisions Made
- Vertical list layout for /writing index (single column, thought leadership feel per plan recommendation)
- 3-column grid for homepage writing preview (compact, scannable)
- Used post.id for href paths (not post.slug, per Astro 5 content collections)

## Deviations from Plan

### Auto-fixed Issues

**1. [Rule 3 - Blocking] Installed missing @astrojs/mdx dependency**
- **Found during:** Task 1 (build verification)
- **Issue:** @astrojs/mdx not installed in worktree, build failed with "Cannot find module '@astrojs/mdx'"
- **Fix:** Ran `npm install @astrojs/mdx`
- **Files modified:** package.json, package-lock.json (not committed -- worktree-local)
- **Verification:** Build succeeds after install
- **Committed in:** N/A (dependency already in main repo, worktree sync issue)

---

**Total deviations:** 1 auto-fixed (1 blocking)
**Impact on plan:** Worktree dependency install needed for build verification. No scope creep.

## Issues Encountered
None beyond the dependency install above.

## User Setup Required
None - no external service configuration required.

## Next Phase Readiness
- Writing system fully functional: content collection, individual post pages (plan 02), index page, and homepage section
- All writing entry points connected: homepage -> /writing -> /writing/[slug]

---
*Phase: 05-writing-blog-system*
*Completed: 2026-03-27*
