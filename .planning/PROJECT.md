# russellkmoore.me

## What This Is

A personal portfolio site for Russell Moore — engineering leader, builder, and AI product creator. The site showcases projects, career highlights, and writing to hiring managers, consulting clients, and technical peers. Dark, modern aesthetic inspired by linear.app and vercel.com.

## Core Value

Visitors immediately understand Russell's expertise and can see tangible proof of what he's built — projects with real metrics, career highlights with real numbers.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Hero section with name, tagline (tech leadership angle, TBD copy), and two anchor CTAs ("See My Work" → Projects, "Let's Talk" → Contact)
- [ ] Subtle animated gradient in hero background (tasteful, not flashy)
- [ ] About section with short narrative + 4 stats cards (20+ Years, $2B+ Platform Scale, $13M ARR Built & Acquired, 200+ Global Team)
- [ ] Projects section as card grid from Astro content collection, with tech stack tags and status badges (Live / In Development)
- [ ] Career section with 3 highlight cards: AT&T, Spark::red, Pivotree — key metrics, not a resume
- [ ] Writing section as card grid from Astro content collection, with graceful "coming soon" state when no published posts
- [ ] Contact section with email + LinkedIn, brief "what I'm open to" copy (full-time roles, consulting, advising, interesting conversations)
- [ ] Sticky nav with name/logo left, section links right, hamburger menu on mobile
- [ ] Minimal footer with copyright + LinkedIn + GitHub icons
- [ ] Reusable components: Card.astro, Badge.astro, SectionHeader.astro
- [ ] Layout.astro wrapping all pages
- [ ] Astro content collections for projects/ and writing/ with defined frontmatter schemas
- [ ] Seed project entries: Mercora and RecompAI
- [ ] Seed writing entries (all draft:true, never rendered): 3 articles
- [ ] Draft filtering — writing with draft:true never renders on the site
- [ ] Dark design: ~#0a0a0f background, violet/purple accent (#7c3aed), off-white text, muted gray secondary
- [ ] Geist Sans font via @fontsource with Inter fallback
- [ ] Card-based layout with grid feel, generous whitespace
- [ ] Fully responsive (mobile-first)
- [ ] Cloudflare Workers deployment via wrangler with @astrojs/cloudflare adapter (output: static)
- [ ] npm scripts: dev, build, deploy
- [ ] README with local dev + deploy instructions

### Out of Scope

- Contact form — unnecessary complexity for v1, email link is sufficient
- CMS integration — content collections with markdown files are enough
- Authentication — no gated content
- RSS feed — v2 idea
- Reading time on articles — v2 idea
- Project demo embeds — v2 idea
- OAuth / social login — no user accounts
- Mobile app — web only

## Context

- Domain: russellkmoore.me (already owned, Cloudflare DNS)
- Audience: hiring managers evaluating leadership fit, consulting clients assessing expertise, technical peers checking credibility
- Russell's background: 20+ years engineering leadership, AT&T ($2B platform), Spark::red (VP Engineering), Pivotree ($13M ARR)
- Current focus: building AI-powered products (Mercora, RecompAI) using Anthropic's ecosystem (Claude API, MCP)
- Hero tagline: tech leadership angle, exact copy TBD — placeholder until Russell finalizes
- All seed writing is draft-only; the Writing section should gracefully show "coming soon"

## Constraints

- **Tech stack**: Astro + TypeScript + Tailwind CSS + @astrojs/cloudflare — decided, no alternatives
- **Deployment**: Cloudflare Workers via wrangler — decided
- **Font**: Geist Sans via @fontsource, Inter fallback — decided
- **Design**: Dark theme with violet accent, linear.app/vercel.com energy — decided
- **Output mode**: Static (output: "static" in Astro config)

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Astro with static output | Fast, simple, perfect for content site | — Pending |
| Content collections over CMS | No runtime dependency, version-controlled content | — Pending |
| Cloudflare Workers deployment | Already on Cloudflare, fast global CDN | — Pending |
| Card-based layout | Modern feel, scannable by busy hiring managers | — Pending |
| No contact form | Email link is simpler and equally effective | — Pending |
| Draft filtering for writing | Ship the section structure now, publish content later | — Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd:transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd:complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-03-26 after initialization*
