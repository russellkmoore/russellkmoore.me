# Phase 1: Foundation & Scaffolding - Context

**Gathered:** 2026-03-26
**Status:** Ready for planning

<domain>
## Phase Boundary

Working Astro project that builds, deploys to Cloudflare Workers, renders a dark-themed page with correct fonts, and serves content from validated collections. No visual components or page sections — those are Phase 2.

</domain>

<decisions>
## Implementation Decisions

### Cloudflare Deployment
- **D-01:** Use `@astrojs/cloudflare` adapter from day one, even though v1 is static content — keeps SSR path ready for future dynamic features (contact form, auth, API routes)
- **D-02:** Configure `wrangler.toml` for russellkmoore.me domain with Workers deployment
- **D-03:** `output: "static"` in Astro config for now; easy to switch to `"server"` or `"hybrid"` later when SSR features are needed

### Astro Configuration
- **D-04:** Lock to Astro 5.x stable (5.18+). Do NOT use Astro 6 beta — adapter compatibility issues
- **D-05:** Content collections config at `src/content.config.ts` (Astro 5+ location, not legacy `src/content/config.ts`)
- **D-06:** Tailwind CSS v4 with `@tailwindcss/vite` plugin — CSS-based config using `@theme` directive, NOT JS tailwind.config.js

### Dark Theme Setup
- **D-07:** Hardcode `class="dark"` on `<html>` element — no toggle, dark is the brand
- **D-08:** Inline `<style>` in `<head>` setting background color to prevent FOUC (white flash on load)
- **D-09:** Theme tokens: background ~#0a0a0f, accent #7c3aed (lighter on hover), off-white primary text, muted gray secondary

### Font Loading
- **D-10:** Use `@fontsource-variable/geist` (variable font, single file covers all weights 100-900) — not `@fontsource/geist-sans` (static weights)
- **D-11:** Inter as CSS fallback font
- **D-12:** Preload font file to prevent layout shift (CLS)

### Content Collection Schemas
- **D-13:** Project status field: enum with 3 values — "Live", "In Development", "Archived"
- **D-14:** Project featured field: boolean (true/false) — featured projects sort first in display
- **D-15:** Project stack field: string array (free-form, e.g., ["Next.js", "Python", "Anthropic API"])
- **D-16:** Project url field: optional string
- **D-17:** Writing tags field: free-form string array (e.g., ["AI", "Leadership", "Commerce"])
- **D-18:** Writing draft field: boolean — draft:true entries never render

### Seed Content
- **D-19:** Two seed projects: Mercora (AI e-commerce, In Development) and RecompAI (AI coaching, In Development)
- **D-20:** Three seed writing entries (all draft:true): "The Case for Executives Who Still Ship Code", "MCP as an Integration Pattern: What I Learned Building Mercora", "Composable Commerce in the AI Era"

### Claude's Discretion
- Exact wrangler.toml configuration details (asset serving, routing)
- npm script implementation (dev/build/deploy commands)
- README structure and content depth
- TypeScript configuration (strict mode, path aliases)
- Exact Tailwind v4 theme token syntax

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Research findings
- `.planning/research/STACK.md` — Verified package versions, Tailwind v4 setup, Astro 5 content collections API
- `.planning/research/PITFALLS.md` — Six critical pitfalls to resolve in this phase (FOUC, font CLS, adapter choice, dark mode config, content collections location, Tailwind v4 migration)
- `.planning/research/ARCHITECTURE.md` — Recommended file structure, component boundaries, build order

### Project context
- `.planning/PROJECT.md` — Project vision, constraints, tech stack decisions
- `.planning/REQUIREMENTS.md` — INFRA-01 through INFRA-08 requirements for this phase

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- None — greenfield project, empty repository

### Established Patterns
- None — patterns will be established in this phase

### Integration Points
- None — this is the foundation phase

</code_context>

<specifics>
## Specific Ideas

- Design energy: linear.app / vercel.com — dark, clean, modern, generous whitespace
- The dark theme is not a preference, it IS the brand identity
- SSR-readiness is future-proofing, not current need — don't over-engineer the Workers config

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 01-foundation-scaffolding*
*Context gathered: 2026-03-26*
