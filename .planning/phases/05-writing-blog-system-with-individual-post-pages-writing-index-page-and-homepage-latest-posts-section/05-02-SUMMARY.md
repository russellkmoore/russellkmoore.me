---
phase: 05-writing-blog-system
plan: 02
subsystem: ui
tags: [astro, content-collections, blog, mdx, dynamic-routes]

requires:
  - phase: 05-01
    provides: PostLayout.astro, writing content collection schema, prose styling
provides:
  - Dynamic route for individual blog post pages at /writing/[slug]
  - Published test post (mcp-integration-pattern) with real content
  - Prev/next post navigation
affects: [05-03, 05-04]

tech-stack:
  added: []
  patterns: [Astro 5 getStaticPaths with post.id, render() from astro:content]

key-files:
  created:
    - src/pages/writing/[...slug].astro
  modified:
    - src/content/writing/mcp-integration-pattern.md

key-decisions:
  - "Used [...slug] rest/spread route for future nested path support"
  - "Prev/next sorted by pubDate descending (newest first)"

patterns-established:
  - "Dynamic route pattern: getStaticPaths + render() from astro:content (Astro 5 API)"
  - "Draft filtering in getStaticPaths callback, not post-query"

requirements-completed: []

duration: 2min
completed: 2026-03-27
---

# Phase 5 Plan 02: Individual Post Pages Summary

**Dynamic route at /writing/[slug] rendering MDX content with PostLayout, prev/next navigation, and one published test post with syntax-highlighted code blocks**

## Performance

- **Duration:** 2 min
- **Started:** 2026-03-27T05:21:35Z
- **Completed:** 2026-03-27T05:23:07Z
- **Tasks:** 1
- **Files modified:** 2

## Accomplishments
- Dynamic route [...slug].astro with getStaticPaths filtering out draft posts
- Published mcp-integration-pattern post with headings, code blocks, lists, and blockquote
- Prev/next navigation at bottom of posts (handles single-post gracefully)

## Task Commits

Each task was committed atomically:

1. **Task 1: Create dynamic post route and publish one test post** - `247c1b7` (feat)

## Files Created/Modified
- `src/pages/writing/[...slug].astro` - Dynamic route with getStaticPaths, draft filtering, prev/next nav
- `src/content/writing/mcp-integration-pattern.md` - Published post with real MCP content, code blocks, lists, blockquote

## Decisions Made
- Used [...slug] rest/spread route pattern for potential future nested paths
- Prev/next navigation sorted by pubDate descending (newest first), with older = prev, newer = next
- Post content written with real substance about MCP patterns (not lorem ipsum)

## Deviations from Plan

### Auto-fixed Issues

**1. [Rule 3 - Blocking] Installed missing @astrojs/mdx dependency**
- **Found during:** Task 1 (build verification)
- **Issue:** @astrojs/mdx referenced in astro.config.mjs but not installed in this worktree
- **Fix:** Ran `npm install @astrojs/mdx`
- **Files modified:** package.json, package-lock.json (not committed -- worktree dependency)
- **Verification:** Build succeeds after install
- **Committed in:** Not committed (local worktree node_modules issue)

---

**Total deviations:** 1 auto-fixed (1 blocking)
**Impact on plan:** Worktree missing dependency from Plan 01. No scope creep.

## Issues Encountered
None beyond the dependency install above.

## User Setup Required
None - no external service configuration required.

## Next Phase Readiness
- Individual post pages working at /writing/[slug]
- Ready for Plan 03 (writing index page) and Plan 04 (homepage latest posts section)
- PostLayout and prose styling from Plan 01 confirmed working

---
*Phase: 05-writing-blog-system*
*Completed: 2026-03-27*
