# Phase 6: Project Detail Pages - Context

**Gathered:** 2026-03-27
**Status:** Ready for planning

<domain>
## Phase Boundary

Individual project detail pages at `/projects/[slug]` with case study content, tech stack breakdowns, screenshots, and challenges/outcomes. Remove hover-expand from homepage project cards (replaced by detail pages). Convert project content to MDX format. No /projects index page — homepage section is the listing.

</domain>

<decisions>
## Implementation Decisions

### Page Content
- **D-01:** Project detail pages contain: extended description, tech stack breakdown (visual with explanations), screenshots/demos, and challenges & outcomes (case study narrative)
- **D-02:** MDX format for project content — same as writing, enables embedded components, code blocks, callouts
- **D-03:** Update project content collection glob to support `.mdx` files (same pattern as writing: `"**/*.{md,mdx}"`)

### Page Layout
- **D-04:** Immersive hero layout (like writing post pages) — full-width gradient hero with project title, status badge, and tech tags at top. Centered content below
- **D-05:** Screenshots section — add an optional `images` field to project schema for screenshot paths

### Navigation & Linking
- **D-06:** Both card clickable + "View project →" link on homepage cards (same pattern as writing PostCard)
- **D-07:** No /projects index page — homepage Projects section is the only listing
- **D-08:** Back navigation: "← Back to Projects" link that scrolls to homepage #projects section

### Homepage Changes
- **D-09:** Remove hover-to-expand body content from homepage project cards — detail pages replace this
- **D-10:** Add "View project →" text link to both featured and non-featured project cards

### Claude's Discretion
- ProjectLayout component design (reuse PostLayout pattern or create separate)
- Prev/next navigation between projects
- Screenshot display format (lightbox, inline, grid)
- Tech stack visual presentation (icons, cards, list)
- Which projects get full case study content vs shorter descriptions

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Existing implementation (modify)
- `src/components/sections/Projects.astro` — Homepage projects section, remove hover-expand, add detail links
- `src/content.config.ts` — Project collection schema, add images field, update glob for MDX
- `src/content/projects/*.md` — Existing project content files (convert to .mdx for full case studies)

### Patterns to follow
- `src/pages/writing/[...slug].astro` — Dynamic route pattern for content collection pages
- `src/layouts/PostLayout.astro` — Immersive hero layout pattern to replicate/adapt
- `src/components/PostCard.astro` — Clickable card + link pattern

### Design system
- `src/styles/global.css` — Theme tokens, prose typography (already configured for MDX)

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- PostLayout.astro — immersive hero pattern, can adapt for ProjectLayout
- Prose typography in global.css — already configured for MDX content
- Card, Badge components — used on homepage, reusable on detail pages
- Icon component — for GitHub/external links

### Established Patterns
- Content collection dynamic routing: `[...slug].astro` with `getStaticPaths()`
- `render()` from `astro:content` for MDX content
- Draft filtering via `getCollection` callback
- OG meta tags per page via Layout.astro props

### Integration Points
- Homepage project cards need to become links to `/projects/[slug]`
- Project .md files need MDX content added (case study sections)

</code_context>

<specifics>
## Specific Ideas

- Mirror the writing blog pattern — consistency across the site
- Case study structure: Overview → Tech Stack → Screenshots → Challenges → Outcomes
- Not all projects need equal depth — Mercora and RecompAI get full case studies, smaller projects can have lighter pages
- Screenshots need to be sourced/created for each project

</specifics>

<deferred>
## Deferred Ideas

- /projects index page (decided against for now)
- Project demo embeds (V2-03 — live embedded demos)

</deferred>

---

*Phase: 06-project-detail-pages*
*Context gathered: 2026-03-27*
