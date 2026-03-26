# Project Research Summary

**Project:** russellkmoore.me personal portfolio
**Domain:** Static engineering leader portfolio site
**Researched:** 2026-03-26
**Confidence:** HIGH

## Executive Summary

This is a static, content-driven personal portfolio site targeting hiring managers and engineering leadership at technical organizations. The expert approach is a fully pre-rendered static site (zero server-side runtime) served from a global CDN, with content managed as version-controlled Markdown. Astro 5 with Tailwind CSS v4 and Cloudflare Workers is the clear recommended stack — Astro's static output model means every page is pre-rendered at build time, Tailwind v4 provides a zero-configuration CSS-first styling approach, and Cloudflare's CDN delivers sub-50ms response times globally. The combination makes achieving a Lighthouse score of 95+ nearly automatic, which itself functions as a credibility signal for an engineering leader.

The recommended approach is a single-page composition of six to seven named sections (Hero, About, Projects, Career, Writing, Contact), assembled in `index.astro` from self-contained section components. Projects and Writing content live in Astro Content Collections (Zod-validated Markdown frontmatter), while static data like career highlights and contact info is hardcoded directly in section components. No client-side framework is needed — zero React/Vue/Svelte. The only client-side JavaScript required is a mobile menu toggle (under 20 lines). This architecture produces a fast, maintainable, zero-dependency production site.

The key risks are all concentrated in Phase 1 scaffolding decisions that are expensive to reverse later. Three critical configuration mistakes are common: (1) installing the `@astrojs/cloudflare` adapter for a site that does not need SSR, which introduces unnecessary complexity and known build failures; (2) misconfiguring Tailwind v4 dark mode (the JavaScript `darkMode` config key no longer exists — it requires a CSS-first `@custom-variant` declaration); and (3) placing the Content Collections config at the legacy `src/content/config.ts` path instead of the Astro v5 location `src/content.config.ts`. All three must be solved before any component work begins.

## Key Findings

### Recommended Stack

Astro 5.18 (latest stable) with TypeScript, Tailwind CSS v4.2, and Cloudflare Workers via Wrangler v4 is the production-ready combination. Astro 6 is in beta and should not be used — it introduces breaking changes with the Cloudflare adapter and removes the legacy Content Collections API. Tailwind v4 is a ground-up rewrite: CSS-first configuration via `@theme` blocks in `global.css`, no `tailwind.config.js`, and a Vite plugin integration (`@tailwindcss/vite`) that replaces the now-deprecated `@astrojs/tailwind` integration. Cloudflare acquired Astro in January 2026, making Cloudflare Workers the first-class deployment target. For static output specifically, the `@astrojs/cloudflare` adapter is unnecessary and counterproductive — deploy with `wrangler deploy` pointing to `dist/` directly.

**Core technologies:**
- **Astro 5.18**: Static site framework — content collections, built-in Vite, zero-JS static output
- **TypeScript 5.7**: Type safety — bundled with Astro, zero additional config required
- **Tailwind CSS 4.2**: Utility-first styling — 5x faster builds, CSS-first config via `@theme`
- **@tailwindcss/vite 4.2**: Tailwind integration — Vite plugin replacing deprecated @astrojs/tailwind
- **Wrangler 4.77**: Cloudflare deployment CLI — static asset serving via Workers, no adapter needed
- **@fontsource-variable/geist**: Variable font — single file covers all weights 100-900
- **astro-icon + @iconify-json/lucide**: SVG icons — 275K+ icons, zero runtime dependency
- **@astrojs/sitemap**: XML sitemap — auto-generated at build, essential for SEO

### Expected Features

The portfolio audience (hiring managers, engineering leadership) has specific expectations. A site that meets table stakes reads as competent; one that adds the right differentiators reads as excellent. The research identified a clear anti-feature list that prevents common over-engineering traps.

**Must have (table stakes):**
- Hero with name, specific tagline, and two CTAs ("See My Work" / "Let's Talk") — visitors decide in 3-5 seconds
- Projects section with tech stack badges, status indicators, and metrics — the core proof of work
- Career highlights with quantified achievements (scale, revenue, team size) — credibility anchor for leadership roles
- About section with scannable stats cards ($2B+ Platform Scale, $13M ARR, 20+ Years, 200+ Team) — quick-scan context
- Sticky navigation with anchor links to all sections — usability for single-page scroll
- Contact section with email and LinkedIn — conversion point; must be easy to reach
- SEO fundamentals: title tags, meta descriptions, Open Graph tags — LinkedIn share previews matter
- Dark theme with polish — tech audience expects it; poor execution reads as "template site"
- Mobile-first responsive layout — hiring managers browse on phones
- Semantic HTML and accessibility — signals technical competence

**Should have (differentiators):**
- Animated hero gradient — signals polish, not decoration
- Stats cards with real numbers prominently displayed — "shows $2B Platform Scale" is rare and memorable
- Project status badges (Live / In Development) — signals active builder mentality
- Writing section with graceful "coming soon" state — positions as thought leader even before first post
- Subtle CSS micro-interactions — hover states, smooth scroll; CSS transitions only, no heavy JS
- Performance as proof — a perfect Lighthouse score is itself a portfolio piece

**Defer to v2+:**
- RSS feed — no published posts to syndicate yet
- Individual blog post pages — ship when actual posts are published; draft filtering handles the interim
- Individual project detail pages — card + external link is sufficient for now
- Blog reading time indicators — not needed until posts exist
- Project demo embeds — links to live sites are sufficient

**Deliberate anti-features (do not build):**
- Contact form — spam magnet, needs backend; mailto: is what hiring managers prefer
- Dark/light mode toggle — adds complexity and flash-of-wrong-theme; dark IS the brand
- CMS or admin panel — over-engineering for fewer than 20 content items
- Skill bars or percentage ratings — cringe, meaningless; projects demonstrate skill
- Testimonials — LinkedIn handles endorsements better
- Chatbot or AI assistant — gimmicky on a personal site

### Architecture Approach

The architecture is a build-time composition pipeline with no runtime complexity. Markdown source files are validated through Zod schemas at build time, flow through Astro's Content Collections API (`getCollection()`), and are rendered by section components into static HTML. The `dist/` output is uploaded to Cloudflare Workers as static assets. There is no database, no API, no server process. The only interactive element is a mobile menu toggle handled by a small inline script.

The separation between `src/components/` (reusable UI primitives: Card, Badge, SectionHeader, Nav, Footer) and `src/sections/` (page-level compositions: Hero, About, Projects, Career, Writing, Contact) is the key architectural decision. Sections import components; components never import sections. This prevents the common problem of a flat 15+ component folder with mixed abstraction levels.

**Major components:**
1. `Layout.astro` — HTML shell, head meta, nav, footer; wraps all content via `<slot />`
2. `src/pages/index.astro` — single-page composition; imports and sequences all section components
3. Section components (Hero, About, Projects, Career, Writing, Contact) — self-contained, own their data fetching
4. Reusable UI components (Card, Badge, SectionHeader, Nav, Footer, MobileMenu) — props-driven, zero JS
5. Content Collections (`src/content.config.ts`) — Zod schemas for projects and writing Markdown files
6. `global.css` — Tailwind `@import`, `@theme` tokens, font imports; single source of design truth
7. `wrangler.toml` — Cloudflare Workers deployment pointing to `dist/`

### Critical Pitfalls

1. **Installing `@astrojs/cloudflare` adapter for static output** — skip the adapter entirely; configure `wrangler.jsonc` with `assets.directory: "./dist"` and deploy with `wrangler deploy`. The adapter is for SSR/hybrid rendering only and causes known build failures with static output.

2. **Tailwind v4 dark mode misconfiguration** — the `darkMode: 'class'` JS config key no longer exists. Add `@custom-variant dark (&:where(.dark, .dark *));` to `global.css` and hardcode `class="dark"` on `<html>` in `Layout.astro`. Without this, all `dark:` utility classes silently do nothing.

3. **Content Collections config at wrong path** — Astro v5 moved config from `src/content/config.ts` to `src/content.config.ts`. Every collection must use a `glob()` loader (no implicit folder-based loading). Using `slug` instead of `id` is a related mistake — `slug` no longer exists in the v5 Content Layer API.

4. **Dark theme flash of unstyled content (FOUC)** — hardcode `<style>html { background-color: #0a0a0f; }</style>` in `<head>` in `Layout.astro`. If a theme toggle is ever added, use an `is:inline` script to read `localStorage` before first paint. FOUC on a dark-only site looks unprofessional to the exact audience this site is targeting.

5. **Portfolio content that fails the 6-second scan** — hero must show name + specific tagline + CTAs above the fold; stats cards must be immediately visible; projects must show tech stack and status on the card itself without requiring a click. The squint test: blur your eyes and the hierarchy should still communicate competence. This is harder to fix after all components are built.

## Implications for Roadmap

Based on the research dependency graph and pitfall phase mapping, a three-phase structure is recommended. All critical configuration decisions belong in Phase 1 because they are cross-cutting — a wrong choice here contaminates every subsequent component.

### Phase 1: Foundation and Scaffolding

**Rationale:** All six critical pitfalls have a "Phase 1" prevention window. The Tailwind dark mode variant, Content Collections file location, Cloudflare adapter decision, FOUC prevention, and font loading strategy must all be resolved before any section or component work begins. These are infrastructure decisions that are expensive to retrofit.

**Delivers:** Working dev environment, correct build pipeline, deploying to Cloudflare Workers, design tokens established, zero pitfalls carried forward.

**Includes:**
- Project initialization (`npm create astro@latest`, TypeScript, Tailwind v4 via `@tailwindcss/vite`)
- `astro.config.mjs` with correct configuration (no adapter, sitemap, astro-icon)
- `wrangler.toml` with static assets pointing to `dist/`
- `global.css` with `@import "tailwindcss"`, `@custom-variant dark`, `@theme` tokens
- `Layout.astro` with `<html class="dark">`, inline background-color style (FOUC prevention), font preload links
- Font imports (`@fontsource-variable/geist`, `@fontsource/inter` fallback)
- `src/content.config.ts` with Zod schemas for projects and writing collections
- Seed content files (2 draft project entries, 2-3 draft writing entries) to validate collections
- `tsconfig.json`, Prettier with `prettier-plugin-astro` and `prettier-plugin-tailwindcss`
- Verified: `astro build` succeeds, `wrangler deploy` succeeds, no white flash, `getCollection()` returns entries

**Avoids:** All six critical pitfalls (Tailwind dark mode, content config path, Cloudflare adapter, FOUC, font CLS, scanability planning).

### Phase 2: Components and Sections

**Rationale:** Once the foundation is correct, component work proceeds linearly following the feature dependency graph. Reusable UI primitives (Card, Badge, SectionHeader) must exist before sections can use them. Navigation requires section IDs on all sections.

**Delivers:** Complete single-page portfolio — all sections rendered with real content, mobile-responsive, accessible, passing the 6-second scan test.

**Includes:**
- Reusable components: Card, Badge, SectionHeader, Nav (sticky), Footer, MobileMenu
- All sections with real content: Hero (animated gradient + CTAs), About (stats cards), Projects (from content collections), Career (AT&T, Spark::red, Pivotree with metrics), Writing (graceful "coming soon"), Contact
- `index.astro` assembling all sections
- Smooth scroll, anchor links, mobile hamburger menu (inline JS, under 20 lines)
- Animated hero gradient (CSS, performant, no JS animation libraries)
- SEO meta tags and Open Graph in `Layout.astro`
- Real project Markdown files (`mercora.md`, `recompai.md`) replacing seed content
- Draft filtering verified: `draft: true` entries absent from `dist/` output

**Implements:** Single-page section composition pattern, props-driven reusable components, content collections with draft filtering, all from ARCHITECTURE.md.

**Avoids:** Mobile menu tap target < 44px, "Coming Soon" writing section that looks broken, stats cards without context labels, contact section inaccessible without scrolling.

### Phase 3: Polish and Production Readiness

**Rationale:** Performance optimization, SEO finalization, and production verification are best done once the site is feature-complete. Lighthouse scores, OG image creation, and the "looks done but isn't" checklist require a real built artifact to test against.

**Delivers:** Production-ready deployment with 95+ Lighthouse score, correct OG preview on LinkedIn, custom 404 page, verified deploy pipeline.

**Includes:**
- Lighthouse audit and optimization (CLS < 0.1, LCP < 2s, 0 accessibility errors)
- OG image (`public/og-image.png`) designed and referenced in meta tags
- Custom `404.astro` page with site styling (not Cloudflare default error)
- Cloudflare Analytics setup (privacy-respecting, no JS required)
- Final "looks done but isn't" checklist verification: dark theme flash on throttled connection, mobile menu on real device, draft filtering in `dist/`, font loading, deploy from clean checkout, all anchor links with sticky nav offset
- `sitemap.xml` verified in build output

### Phase Ordering Rationale

- Phase 1 before everything because pitfalls 1-5 all require Phase 1 prevention; retrofitting them into an existing component tree adds friction and risk.
- Phase 2 follows the feature dependency graph from FEATURES.md exactly: Layout shell → reusable primitives → sections → page assembly.
- Phase 3 is last because Lighthouse scores, OG previews, and the "looks done but isn't" checklist require a complete, buildable site.
- The architecture's build-order dependency graph (ARCHITECTURE.md Phase 1-6) maps cleanly to this three-phase roadmap structure.

### Research Flags

Phases with standard, well-documented patterns — research-phase not needed:
- **Phase 1:** All configuration decisions are resolved by research; specific values are known.
- **Phase 2:** Component patterns are standard Astro idioms; content is pre-written; no unknowns.
- **Phase 3:** Lighthouse optimization and Cloudflare deployment are well-documented.

No phases require deeper `/gsd:research-phase` during planning. The research corpus is comprehensive enough to proceed directly to roadmap creation.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | All versions verified against npm registries and official release blogs; version compatibility matrix confirmed |
| Features | HIGH | Synthesized from multiple portfolio analysis sources; anti-features based on clear engineering reasoning |
| Architecture | HIGH | Based on official Astro docs and real GitHub issues; build-order dependency graph is deterministic |
| Pitfalls | HIGH | Each pitfall traced to specific GitHub issues, changelogs, or official migration guides; recovery costs assessed |

**Overall confidence:** HIGH

### Gaps to Address

- **OG image design**: The research identifies that an OG image is needed (`public/og-image.png`) but does not specify what it should look like. This is a design decision, not a research gap — resolve during Phase 3.
- **Animated gradient implementation**: Research recommends CSS-based animation for the hero gradient but does not specify the exact technique (conic-gradient animation, background-position shift, canvas). The constraint is clear (performant, no JS animation library, no `background-position` animation which triggers repaints) — specific implementation is a Phase 2 decision.
- **Exact project card content**: Research confirms the content schema but actual copywriting for Mercora and RecompAI project cards is not in scope for research. Resolve during Phase 2 content authoring.
- **Font preload path**: The Fontsource package buries woff2 files at a path like `/fonts/geist-sans-latin-wght-normal.woff2`. The exact filename must be verified from the installed package before adding the `<link rel="preload">` tag. Low risk — one file path lookup during Phase 1.

## Sources

### Primary (HIGH confidence)
- [Astro npm](https://www.npmjs.com/package/astro) — v5.18.0 confirmed latest stable
- [Astro 6 Beta announcement](https://astro.build/blog/astro-6-beta/) — confirmed beta status
- [@astrojs/cloudflare npm](https://www.npmjs.com/package/@astrojs/cloudflare) — v12.6.12 for Astro 5
- [Tailwind CSS v4.0 release](https://tailwindcss.com/blog/tailwindcss-v4) — CSS-first config confirmed
- [Astro Tailwind docs](https://docs.astro.build/en/guides/integrations-guide/tailwind/) — @astrojs/tailwind deprecated for v4
- [Astro Content Collections docs](https://docs.astro.build/en/guides/content-collections/) — v5 API and file location
- [Astro Project Structure docs](https://docs.astro.build/en/basics/project-structure/)
- [Deploy Astro to Cloudflare docs](https://docs.astro.build/en/guides/deploy/cloudflare/)
- [Cloudflare adapter static output issue #15650](https://github.com/withastro/astro/issues/15650) — confirmed fixed
- [Astro v6 Upgrade Guide](https://docs.astro.build/en/guides/upgrade-to/v6/) — breaking changes documented
- [Tailwind CSS Dark Mode Docs](https://tailwindcss.com/docs/dark-mode) — @custom-variant requirement
- [Wrangler v4 changelog](https://developers.cloudflare.com/changelog/post/2025-03-13-wrangler-v4/) — v4 features

### Secondary (MEDIUM confidence)
- [Engineer Portfolios: 20+ Examples (2026)](https://www.sitebuilderreport.com/inspiration/engineer-portfolios) — table stakes features
- [21 Best Developer Portfolio Websites (2026)](https://colorlib.com/wp/developer-portfolios/) — differentiator patterns
- [Fontaine for CLS Reduction in Astro](https://eatmon.co/blog/using-fontaine-with-astro) — font CLS mitigation technique
- [Astro Dark Mode FOUC Prevention](https://axellarsson.com/blog/astrojs-prevent-dark-mode-flicker/) — is:inline script pattern
- [Migrating Content Collections Astro 4 to 5](https://chenhuijing.com/blog/migrating-content-collections-from-astro-4-to-5/) — API change confirmation
- [Portfolio Homepage Mistakes (UX Playbook)](https://uxplaybook.org/articles/6-ux-portfolio-homepage-mistakes-2025) — 6-second scan research
- [Tailwind v4 Dark Mode Discussion](https://github.com/tailwindlabs/tailwindcss/discussions/16517) — @custom-variant approach

---
*Research completed: 2026-03-26*
*Ready for roadmap: yes*
