# russellkmoore.me

## What This Is

A personal portfolio site for Russell Moore — engineering leader, builder, and AI product creator. Full-featured Astro 5 static site with dark theme, MDX-powered blog and project case studies, career timelines, and a 6-component MDX library. Deployed to Cloudflare Workers.

## Core Value

Visitors immediately understand Russell's expertise and can see tangible proof of what he's built — projects with real metrics, career highlights with real numbers.

## Requirements

### Validated

- ✓ Hero section with name, tagline, animated mesh gradient, and two anchor CTAs — v1.0
- ✓ About section with narrative + 4 stats cards (20+ Years, $2B+, $13M ARR, 200+ Global Team) — v1.0
- ✓ Projects section as card grid from content collection with tech stack tags and status badges — v1.0
- ✓ Career section with 4 company cards (AT&T, Spark::red, Pivotree, Black Magic Consulting) and horizontal accomplishment timelines — v1.0
- ✓ Writing section with blog index, individual post pages, and homepage latest posts — v1.0
- ✓ Contact section with email + LinkedIn and "what I'm open to" copy — v1.0
- ✓ Sticky nav with mobile hamburger menu, footer with social icons — v1.0
- ✓ Reusable components: Card, Badge, SectionHeader, TimelineBar, MilestoneNode — v1.0
- ✓ Layout.astro wrapping all pages with SEO meta tags and OG previews — v1.0
- ✓ Content collections for projects/ and writing/ with Zod schemas — v1.0
- ✓ MDX support with Shiki syntax highlighting and prose typography — v1.0
- ✓ Project detail pages at /projects/[slug] with case study content — v1.0
- ✓ 6-component MDX library (Callout, Figure, CodeComparison, ScreenshotCarousel, InteractiveScreenshot, LinkCard) — v1.0
- ✓ Dark theme (#0a0a0f bg, #7c3aed violet accent), Geist Sans font, responsive layout — v1.0
- ✓ Cloudflare Workers deployment via wrangler (static output) — v1.0
- ✓ Accessibility: skip-to-content, focus-visible, keyboard nav, ARIA labels — v1.0

### Active

- [ ] RSS feed for writing posts
- [ ] Reading time estimate on writing entries
- [ ] OG image (deferred from v1.0 — approved without screenshot)

### Out of Scope

- Contact form — email link is sufficient, avoids spam/backend complexity
- CMS integration — content collections with markdown files are enough for <20 items
- Authentication — no gated content needed
- OAuth / social login — no user accounts
- Mobile app — web only
- Dark/light mode toggle — dark theme IS the brand
- Blog comments — link to LinkedIn discussions instead
- Heavy animations (GSAP, parallax) — subtle CSS transitions only

## Context

- Domain: russellkmoore.me (Cloudflare DNS)
- Audience: hiring managers, consulting clients, technical peers
- Russell's background: 20+ years engineering leadership, AT&T ($2B platform), Spark::red (VP Engineering), Pivotree ($13M ARR)
- Current focus: AI-powered products (Mercora, RecompAI) using Anthropic's ecosystem
- Shipped v1.0: 3,688 LOC across Astro/TS/CSS/MDX, 7 project pages, blog system, 6 MDX components
- Tech stack: Astro 5.18, Tailwind CSS v4, TypeScript, @astrojs/mdx, Cloudflare Workers

## Constraints

- **Tech stack**: Astro + TypeScript + Tailwind CSS + @astrojs/cloudflare — decided, no alternatives
- **Deployment**: Cloudflare Workers via wrangler — decided
- **Font**: Geist Sans via @fontsource, Inter fallback — decided
- **Design**: Dark theme with violet accent, linear.app/vercel.com energy — decided
- **Output mode**: Static (output: "static" in Astro config)

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Astro with static output | Fast, simple, perfect for content site | ✓ Good — fast builds, zero JS by default |
| Content collections over CMS | No runtime dependency, version-controlled content | ✓ Good — Zod schemas catch errors at build |
| Cloudflare Workers deployment | Already on Cloudflare, fast global CDN | ✓ Good — seamless deploy via wrangler |
| Card-based layout | Modern feel, scannable by busy hiring managers | ✓ Good — clean grid layout |
| No contact form | Email link is simpler and equally effective | ✓ Good — no spam concerns |
| Draft filtering for writing | Ship the section structure now, publish content later | ✓ Good — graceful "coming soon" state |
| No @astrojs/cloudflare for static | SSR-only adapter, not needed for static output | ✓ Good — simpler config |
| Tailwind v4 CSS-based config | @theme tokens in CSS instead of JS config file | ✓ Good — faster builds, cleaner setup |
| JS tooltips for timelines | CSS approach had overflow clipping issues | ✓ Good — fixed-position tooltips work reliably |
| MDX auto-registration via barrel | Components prop on Content render, barrel import | ✓ Good — DRY, no per-page imports needed |
| Career data hardcoded | Only 4 static entries, not worth a content collection | ✓ Good — simple and fast |
| Singleton dialog per carousel | One dialog instance per ScreenshotCarousel | ✓ Good — avoids DOM bloat |

---
*Last updated: 2026-04-09 after v1.0 milestone*
