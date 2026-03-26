# Pitfalls Research

**Domain:** Astro portfolio site on Cloudflare Workers (static output, Tailwind dark theme, content collections)
**Researched:** 2026-03-26
**Confidence:** HIGH

## Critical Pitfalls

### Pitfall 1: Tailwind v4 Dark Mode Requires Explicit CSS-First Configuration

**What goes wrong:**
Dark mode `dark:` utility classes silently do nothing. The site renders as if dark classes do not exist. Developers spend hours debugging DOM class toggling when the real issue is Tailwind configuration.

**Why it happens:**
Tailwind v4 dropped the `darkMode: 'class'` JavaScript config key entirely. Dark mode now requires a CSS-first declaration via `@custom-variant`. Without it, the `dark:` variant never activates even if `class="dark"` is on the `<html>` element. Since this project is dark-only (no toggle), developers may assume `dark:` classes just work with the OS preference -- but OS-preference-based dark mode uses `prefers-color-scheme`, not a class. If you want class-based control (recommended for a dark-only site), you must explicitly configure it.

**How to avoid:**
In your main CSS file (e.g., `src/styles/global.css`), add the custom variant declaration:

```css
@import "tailwindcss";
@custom-variant dark (&:where(.dark, .dark *));
```

Then ensure `<html class="dark">` is always set in your `Layout.astro`. For a dark-only site, hardcode the class -- no toggle logic needed. Do NOT use the deprecated `@astrojs/tailwind` integration; use the `@tailwindcss/vite` plugin directly.

**Warning signs:**
- `dark:bg-*` classes visibly ignored in the browser
- Elements rendering with light/default backgrounds despite dark classes
- Inspecting elements shows the Tailwind class is present but no CSS rule matches

**Phase to address:**
Phase 1 (Project scaffolding) -- this must be configured before any component styling begins.

---

### Pitfall 2: Content Collections Config File Location Changed in Astro v5/v6

**What goes wrong:**
Content collections silently fail to load or throw build errors. The `getCollection()` call returns empty arrays. Developers following outdated tutorials place the config at `src/content/config.ts` (the old location) instead of the new location.

**Why it happens:**
Astro v5 moved the content collections config from `src/content/config.ts` to `src/content.config.ts` (project root of `src/`). Astro v6 removed the legacy Content Collections API entirely -- all collections must use the Content Layer API with a `loader` property. Most tutorials and blog posts still reference the old file location and old API shape.

**How to avoid:**
- Place config at `src/content.config.ts` (NOT `src/content/config.ts`)
- Use the Content Layer API with `glob()` loader:

```typescript
import { defineCollection, z } from 'astro:content';
import { glob } from 'astro/loaders';

const projects = defineCollection({
  loader: glob({ base: './src/content/projects', pattern: '**/*.md' }),
  schema: z.object({
    title: z.string(),
    // ...
  }),
});
```

- Every collection must have a `loader` -- there is no implicit folder-based loading
- The `slug` field no longer exists; use `id` instead
- The `layout` frontmatter field is not supported in collection entries; apply layouts at the page route level

**Warning signs:**
- `getCollection('projects')` returns `[]` despite files existing in `src/content/projects/`
- Build warnings about deprecated content collections API
- TypeScript errors about missing `loader` property

**Phase to address:**
Phase 1 (Project scaffolding) -- content collection schema must be correctly set up before any content is authored.

---

### Pitfall 3: Static Output Does Not Need the Cloudflare Adapter

**What goes wrong:**
Developers install `@astrojs/cloudflare` for a purely static site, then encounter confusing build errors, unnecessary Worker bundles, and deployment failures. The adapter introduces complexity (server directory generation, wrangler config redirection) that is completely unnecessary for static output.

**Why it happens:**
The PROJECT.md specifies both `@astrojs/cloudflare adapter` AND `output: static`. These are contradictory for a purely static site. The adapter is only needed for SSR/hybrid rendering. For `output: 'static'`, Astro pre-renders all pages at build time and you deploy the `dist/` directory as static assets -- no Worker code needed. There was a specific Astro v6 bug (issue #15650, now fixed) where the adapter with static output caused deployment failures by deleting the server directory that the wrangler config pointed to.

**How to avoid:**
Do NOT install `@astrojs/cloudflare`. Instead:
1. Use default Astro static output (no adapter)
2. Configure `wrangler.jsonc` to serve static assets:

```jsonc
{
  "name": "russellkmoore-me",
  "compatibility_date": "2026-03-26",
  "assets": {
    "directory": "./dist"
  }
}
```

3. Deploy with `wrangler deploy` -- it uploads static files to Cloudflare's edge network
4. If SSR is needed later (v2+), then add the adapter

**Warning signs:**
- Build output includes a `dist/server/` directory for a static-only site
- Wrangler errors about missing config paths or worker bundles
- Deployment creates a Worker when you only need static asset serving

**Phase to address:**
Phase 1 (Project scaffolding) -- get the build pipeline right from the start. This is the single most important architectural decision to get right early.

---

### Pitfall 4: Dark Theme Flash of Unstyled Content (FOUC)

**What goes wrong:**
On page load, the site briefly flashes white/light before the dark theme applies. For a dark-only portfolio site targeting hiring managers, this looks unprofessional and signals poor attention to detail.

**Why it happens:**
If the `dark` class is applied via JavaScript that runs after the initial paint, there is a visible flash. Even with `class="dark"` hardcoded in the HTML template, external CSS files may load after the initial render. Astro's default script handling bundles and defers scripts, which delays execution.

**How to avoid:**
For a dark-only site (no theme toggle), the simplest prevention is:
1. Hardcode `class="dark"` on `<html>` in `Layout.astro`
2. Set the background color directly in a `<style>` tag in the `<head>` (not via Tailwind utility classes alone):

```html
<style>
  html { background-color: #0a0a0f; }
</style>
```

3. If you ever add a theme toggle later, use an `is:inline` script in `<head>` to read localStorage before paint:

```html
<script is:inline>
  if (localStorage.theme === 'dark' || !localStorage.theme) {
    document.documentElement.classList.add('dark');
  }
</script>
```

The `is:inline` directive prevents Astro from bundling/deferring the script.

**Warning signs:**
- Brief white flash visible on page load, especially on slower connections
- Flash reappears on navigation when using View Transitions

**Phase to address:**
Phase 1 (Layout component creation) -- must be baked into the Layout.astro from the start.

---

### Pitfall 5: Font Loading Causes Cumulative Layout Shift (CLS)

**What goes wrong:**
Text renders in a fallback font (e.g., system sans-serif), then visibly reflows when Geist Sans loads. This creates a jarring shift that hurts Lighthouse scores and perceived quality -- particularly bad for a portfolio where first impressions matter.

**Why it happens:**
Fontsource packages bundle font files that load asynchronously. Without explicit `font-display` strategy and fallback font metrics, the browser shows fallback text and then swaps, causing layout shift because Geist Sans and the fallback have different metrics (ascenders, descenders, line height).

**How to avoid:**
1. Import Geist Sans from `@fontsource-variable/geist-sans` in your layout
2. Use `font-display: swap` (Fontsource default, but verify)
3. Preload the primary font weight in `<head>`:

```html
<link rel="preload" href="/fonts/geist-sans-latin-wght-normal.woff2"
      as="font" type="font/woff2" crossorigin />
```

4. Consider using Fontaine (Vite plugin) to generate size-adjusted fallback font metrics that match Geist Sans dimensions, reducing CLS from ~0.4 to ~0.06
5. Set `font-family` with Inter as explicit fallback: `font-family: 'Geist Sans Variable', 'Inter', system-ui, sans-serif`

**Warning signs:**
- Lighthouse CLS score above 0.1
- Visible text reflow on first page load
- Different text width/height before and after font loads

**Phase to address:**
Phase 1 (Layout and typography setup) -- font strategy must be decided during initial scaffolding.

---

### Pitfall 6: Portfolio Content That Fails the 6-Second Scan

**What goes wrong:**
Hiring managers spend 6-8 seconds on initial portfolio scans before deciding to dig deeper. A portfolio that buries key information, loads slowly, or presents walls of text gets closed immediately. The technical implementation may be perfect but the site fails its actual purpose.

**Why it happens:**
Developer portfolios over-optimize for technical elegance and under-optimize for scanability. Common mistakes: hero section with vague tagline and no immediate proof of competence, projects section that requires clicking through to see metrics, career section that reads like a resume instead of highlighting outcomes.

**How to avoid:**
- Hero: Name + specific tagline + two clear CTAs visible above the fold
- Stats cards: Immediately visible numbers (20+ Years, $2B+, $13M ARR, 200+ Team) -- these are the hook
- Projects: Show tech stack tags and status badges on the card itself, not behind a click
- Career highlights: Lead with metrics (e.g., "$2B platform scale"), not job titles
- Contact: Always visible or one click away -- never hidden
- Test with the "squint test": blur your eyes and see if the hierarchy/structure still communicates competence

**Warning signs:**
- You cannot articulate what the site communicates in the first 3 seconds of viewing
- Key metrics require scrolling or clicking to discover
- The site looks like a generic template rather than reflecting the person's specific brand

**Phase to address:**
Phase 2 (Content and component implementation) -- but the information architecture should be planned in Phase 1.

---

## Technical Debt Patterns

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Hardcoded content instead of collections | Faster initial build | Must rewrite to add/edit content; no schema validation | Never -- collections are simple enough to set up from day 1 |
| Inline styles instead of Tailwind utilities | Quick fixes for edge cases | Inconsistent styling, harder to maintain responsive design | Only for the critical-path `<style>` in `<head>` for FOUC prevention |
| Skipping TypeScript in content schemas | Faster prototyping | Frontmatter typos silently break rendering; no autocomplete | Never -- Zod schemas catch errors at build time |
| Single-file components (everything in one .astro file) | Fast MVP | Impossible to reuse, difficult to maintain | Only for truly one-off page sections during initial prototyping |

## Integration Gotchas

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| Cloudflare Workers (static) | Installing `@astrojs/cloudflare` adapter | Skip the adapter entirely; configure `wrangler.jsonc` with `assets.directory: "./dist"` |
| Tailwind v4 + Astro | Using deprecated `@astrojs/tailwind` integration | Use `@tailwindcss/vite` plugin directly in `astro.config.mjs` |
| Fontsource | Importing fonts in individual components | Import once in `Layout.astro`; preload the woff2 file in `<head>` |
| Wrangler deploy | Running `wrangler pages deploy` (Pages API) | Use `wrangler deploy` for Workers-based static asset serving |
| Content collections (Astro v6) | Using `type: 'content'` without `loader` | Use `glob()` loader from `astro/loaders`; define in `src/content.config.ts` |

## Performance Traps

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Unoptimized hero gradient animation | Janky animation, high GPU usage on mobile | Use CSS `will-change: transform` sparingly; prefer `opacity` and `transform` animations over `background-position` | Immediately on low-end mobile devices |
| Large unoptimized images in project cards | Slow LCP, poor mobile performance | Use Astro `<Image>` component for automatic optimization; serve WebP/AVIF; set explicit `width`/`height` | When adding project screenshots |
| Too many font weights loaded | Extra 200-400KB download, delayed text rendering | Load only the weights used (400, 500, 600 at most); use variable font to cover the range in one file | Noticeable on 3G connections |
| No asset caching headers | Repeat visitors re-download everything | Cloudflare Workers static assets get default caching; verify with `Cache-Control` headers | Not a scaling issue but a repeat-visit performance issue |

## Security Mistakes

| Mistake | Risk | Prevention |
|---------|------|------------|
| Exposing draft content via direct URL guessing | Unpublished writing visible to the public | Filter `draft: true` entries in ALL code paths, not just the listing page; verify drafts are excluded at build time |
| Email address in plain text HTML | Spam harvesting | Use `mailto:` link (acceptable for portfolio); optionally obfuscate with CSS or JS, but do not over-engineer |
| Leaking source maps in production | Exposing source code structure | Astro does not include source maps in production builds by default; verify `dist/` contains no `.map` files |

## UX Pitfalls

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| Animated gradient that is too flashy or distracting | Visitor focuses on animation instead of content; looks amateurish | Subtle, slow-moving gradient; test by asking "does this distract from the text?" |
| Mobile hamburger menu that is hard to tap | Hiring managers on phones cannot navigate | Minimum 44x44px tap target; test on real devices, not just responsive mode |
| "Coming Soon" writing section that looks broken | Signals an unfinished site | Style it intentionally: "Writing -- Coming Soon" with a brief note, not an empty grid |
| Stats cards without context | Numbers alone are not meaningful: "$2B" of what? | Each stat needs a brief label explaining what it measures |
| Contact section buried at bottom with no other access | Hiring manager who decides early to reach out must scroll all the way down | Include contact CTA in nav or hero; sticky nav should have a contact link |

## "Looks Done But Isn't" Checklist

- [ ] **Dark theme:** Verify no white flash on fresh load (hard refresh, throttled connection) -- test in Chrome, Safari, Firefox
- [ ] **Responsive nav:** Test hamburger menu actually opens/closes on real mobile; test all section anchor links scroll correctly
- [ ] **Content filtering:** Verify `draft: true` posts do NOT appear anywhere on the built site (check `dist/` output)
- [ ] **Font loading:** Run Lighthouse and verify CLS < 0.1; check that Geist Sans actually loads (not silently falling back to Inter)
- [ ] **Deploy pipeline:** Verify `npm run build && wrangler deploy` works from a clean checkout (no local-only dependencies)
- [ ] **404 page:** Navigate to a non-existent URL and verify a styled 404 appears, not a Cloudflare default error page
- [ ] **Meta tags:** Check `<title>`, `<meta description>`, and Open Graph tags render correctly -- these matter for LinkedIn shares
- [ ] **Anchor links:** All nav section links (#projects, #career, etc.) scroll to the correct section with offset for sticky nav
- [ ] **Mobile performance:** Test on real mobile device or throttled DevTools; animated gradient should not cause jank

## Recovery Strategies

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| Wrong content collection API (legacy) | MEDIUM | Move config to `src/content.config.ts`, add `glob()` loaders, replace `slug` with `id` in all templates |
| Installed Cloudflare adapter unnecessarily | LOW | `npm uninstall @astrojs/cloudflare`, remove from `astro.config.mjs`, add `wrangler.jsonc` with assets config |
| Tailwind dark mode not configured | LOW | Add `@custom-variant dark` to CSS file; verify `<html class="dark">` is set |
| FOUC on dark theme | LOW | Add inline `<style>` with background color + `is:inline` script to `<head>` in Layout |
| Font CLS issues | MEDIUM | Add font preloading, install Fontaine plugin, adjust fallback font metrics |
| Portfolio content fails scanability | HIGH | Requires rethinking information architecture and component hierarchy -- harder to fix after all components are built |

## Pitfall-to-Phase Mapping

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| Tailwind v4 dark mode config | Phase 1: Scaffolding | `dark:bg-*` classes render correctly in dev |
| Content collection config location | Phase 1: Scaffolding | `getCollection()` returns seeded entries |
| Static output without adapter | Phase 1: Scaffolding | `wrangler deploy` succeeds; no Worker bundle in output |
| Dark theme FOUC | Phase 1: Layout | Hard-refresh shows no white flash |
| Font CLS | Phase 1: Layout | Lighthouse CLS < 0.1 |
| Draft content filtering | Phase 2: Content | `draft: true` entries absent from `dist/` |
| Portfolio scanability | Phase 2: Components | Squint test passes; key info visible in 6 seconds |
| Mobile responsiveness | Phase 2: Components | Real device testing passes |
| 404 page | Phase 2: Polish | Custom 404 renders on invalid routes |
| Meta/OG tags | Phase 2: Polish | LinkedIn link preview shows correct title/description/image |

## Sources

- [Astro Cloudflare Deployment Docs](https://docs.astro.build/en/guides/deploy/cloudflare/)
- [@astrojs/cloudflare Integration Guide](https://docs.astro.build/en/guides/integrations-guide/cloudflare/)
- [Cloudflare Workers Astro Framework Guide](https://developers.cloudflare.com/workers/framework-guides/web-apps/astro/)
- [Astro v6 Static + Cloudflare Deployment Bug (fixed)](https://github.com/withastro/astro/issues/15650)
- [Astro v6 Upgrade Guide](https://docs.astro.build/en/guides/upgrade-to/v6/)
- [Astro Content Collections Docs](https://docs.astro.build/en/guides/content-collections/)
- [Tailwind v4 Dark Mode Discussion](https://github.com/tailwindlabs/tailwindcss/discussions/16517)
- [Tailwind CSS Dark Mode Docs](https://tailwindcss.com/docs/dark-mode)
- [Astro Dark Mode FOUC Prevention](https://axellarsson.com/blog/astrojs-prevent-dark-mode-flicker/)
- [FOUC with Astro Transitions and Tailwind](https://www.simonporter.co.uk/posts/what-the-fouc-astro-transitions-and-tailwind/)
- [Fontaine for CLS Reduction in Astro](https://eatmon.co/blog/using-fontaine-with-astro)
- [Astro Font Loading Docs](https://docs.astro.build/en/guides/fonts/)
- [Portfolio Homepage Mistakes (UX Playbook)](https://uxplaybook.org/articles/6-ux-portfolio-homepage-mistakes-2025)
- [Migrating Content Collections Astro 4 to 5](https://chenhuijing.com/blog/migrating-content-collections-from-astro-4-to-5/)

---
*Pitfalls research for: Astro + Tailwind + Cloudflare Workers portfolio site*
*Researched: 2026-03-26*
