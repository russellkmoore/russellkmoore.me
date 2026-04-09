---
phase: 01-foundation-scaffolding
plan: 02
subsystem: content
tags: [astro, content-collections, zod, markdown, glob-loader]

requires:
  - phase: 01-foundation-scaffolding/01
    provides: Layout.astro, global.css theme tokens, Astro project scaffold
provides:
  - Content collection schemas (projects + writing) with Zod validation
  - Seed project entries (Mercora, RecompAI) with typed frontmatter
  - Seed writing entries (3 draft articles)
  - Index page rendering collections via getCollection()
  - README with dev setup and deploy documentation
affects: [02-components-content, 03-polish-deploy]

tech-stack:
  added: []
  patterns: [content-collections-glob-loader, draft-filtering, typed-frontmatter]

key-files:
  created:
    - src/content.config.ts
    - src/content/projects/mercora.md
    - src/content/projects/recompai.md
    - src/content/writing/executives-who-ship-code.md
    - src/content/writing/mcp-integration-pattern.md
    - src/content/writing/composable-commerce-ai.md
    - README.md
  modified:
    - src/pages/index.astro

key-decisions:
  - "Content config at src/content.config.ts (Astro 5 location, not src/content/config.ts)"
  - "Glob loader for both collections with pattern **/*.md"
  - "Draft filtering via getCollection second arg callback"

patterns-established:
  - "Content collections: glob loader at src/content.config.ts with Zod schemas"
  - "Draft filtering: getCollection('writing', ({ data }) => !data.draft)"
  - "Project schema: title, description, stack[], status enum, url?, featured, order"

requirements-completed: [INFRA-04, INFRA-05, INFRA-06, INFRA-08]

duration: 2min
completed: 2026-03-27
---

# Phase 1 Plan 2: Content Collections & Seed Data Summary

**Astro content collections with glob loaders, Zod schemas for projects/writing, seed entries, and proof-of-concept index page rendering collections end-to-end**

## Performance

- **Duration:** 2 min
- **Started:** 2026-03-27T00:29:39Z
- **Completed:** 2026-03-27T00:31:59Z
- **Tasks:** 2
- **Files modified:** 8

## Accomplishments
- Content collection schemas with full Zod validation (status enum, featured boolean, stack array, draft filtering)
- Two seed project entries (Mercora, RecompAI) with typed frontmatter
- Three draft writing entries that are correctly filtered from rendered output
- Index page renders project cards with tech stack tags from content collections
- Writing section shows "Coming soon." empty state (all entries are draft:true)
- README documents dev setup, npm scripts, and Cloudflare Workers deployment

## Task Commits

Each task was committed atomically:

1. **Task 1: Create content collection schemas and seed content entries** - `31990cb` (feat)
2. **Task 2: Update index page to render collections and create README** - `1eb4fff` (feat)

## Files Created/Modified
- `src/content.config.ts` - Content collection schemas with glob loaders for projects and writing
- `src/content/projects/mercora.md` - Seed project: AI-native e-commerce platform
- `src/content/projects/recompai.md` - Seed project: AI-powered coaching platform
- `src/content/writing/executives-who-ship-code.md` - Draft article on technical leadership
- `src/content/writing/mcp-integration-pattern.md` - Draft article on MCP integration
- `src/content/writing/composable-commerce-ai.md` - Draft article on composable commerce + AI
- `src/pages/index.astro` - Proof-of-concept page rendering both collections
- `README.md` - Developer documentation with setup and deploy instructions

## Decisions Made
- Content config placed at `src/content.config.ts` (Astro 5+ location) -- avoids empty collection pitfall of old `src/content/config.ts` location
- Used glob loader with `**/*.md` pattern for both collections -- supports nested directories in future
- Draft filtering done via `getCollection` callback rather than post-query filter -- cleaner API, Astro-idiomatic

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered
None.

## User Setup Required
None - no external service configuration required.

## Known Stubs
None - all content entries have real seed data, and the "Coming soon." empty state is intentional design (all writing is draft:true by design).

## Next Phase Readiness
- Content collection pipeline proven end-to-end (markdown -> schema -> getCollection -> HTML)
- Phase 2 can build real components (ProjectCard, WritingCard) on top of these schemas
- Schema fields (stack, status, featured, tags, draft) ready for component consumption

---
*Phase: 01-foundation-scaffolding*
*Completed: 2026-03-27*
