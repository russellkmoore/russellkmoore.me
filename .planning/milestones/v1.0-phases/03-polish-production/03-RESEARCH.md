# Phase 3: Polish & Production - Research

**Researched:** 2026-03-26
**Domain:** SEO metadata, Open Graph social previews, accessibility, Lighthouse optimization
**Confidence:** HIGH

## Summary

Phase 3 is a production-readiness pass on an existing Astro 5 static site. The site already has all content sections built, a working Layout.astro with title/description props, section IDs for anchor navigation, and some ARIA labels on interactive elements. The work is additive -- extending the `<head>` with OG/canonical tags, adding a static OG image, polishing accessibility (skip link, focus styles, heading hierarchy check), tuning the hero animation, and hitting Lighthouse 95+.

The codebase is clean and well-structured. Sitemap is already installed and configured in astro.config.mjs. There are no images to optimize (only SVG icons via astro-icon). The main gaps are: no canonical URL, no OG tags, no focus-visible styles, no skip-to-content link, and no robots.txt. The hero gradient animation runs at 20s (needs 8-12s per user decision).

**Primary recommendation:** This is straightforward HTML/CSS work with no new dependencies. Focus on getting the meta tags right, adding accessibility primitives, and verifying with Lighthouse.

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- **D-01:** OG image is screenshot-based -- take a styled screenshot of the hero section at 1200x630, save as public/og-image.png. Needs manual updating if hero changes
- **D-02:** OG tags in Layout.astro head: og:title, og:description, og:image, og:type (website), og:url. Also twitter:card (summary_large_image)
- **D-03:** Canonical URL via `<link rel="canonical">` pointing to https://russellkmoore.me/ (or configured site URL from astro.config.mjs)
- **D-04:** Page title already set from Phase 1 -- verify it's "Russell Moore -- Engineering Leader & Builder"
- **D-05:** Meta description already set from Phase 1 -- verify it's appropriate for social sharing
- **D-06:** Speed up hero mesh gradient animation from 20s+ to 8-12s cycle. Increase gradient blob size and opacity slightly for more noticeable movement. CSS-only change to existing Hero.astro keyframes
- **D-07:** Claude's discretion for all accessibility work -- proper heading hierarchy, ARIA labels where needed, keyboard navigation, skip-to-content link, focus visible styles. Target WCAG AA compliance without over-engineering

### Claude's Discretion
- Exact heading hierarchy adjustments across sections
- Skip-to-content link implementation
- Focus visible styles approach
- Sitemap generation via @astrojs/sitemap (already installed and configured)
- Any additional Lighthouse optimization (image formats, unused CSS, render-blocking resources)

### Deferred Ideas (OUT OF SCOPE)
None -- discussion stayed within phase scope
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| SEO-01 | Page title, meta description, and canonical URL set | Layout.astro already has title + description. Add `<link rel="canonical">` using `Astro.site`. Verify existing title/description values. |
| SEO-02 | Open Graph tags for LinkedIn/Slack link previews | Add og:title, og:description, og:image, og:type, og:url, and twitter:card to Layout.astro head. Static OG image at public/og-image.png. |
| SEO-03 | Semantic HTML with proper heading hierarchy and accessibility basics | Heading hierarchy is already correct (h1 in Hero, h2 in SectionHeader, h3 in cards). Add skip-to-content link, focus-visible styles, and ARIA enhancements. |
</phase_requirements>

## Standard Stack

No new dependencies needed. Everything required is already installed.

### Already Installed (verify, don't add)
| Library | Installed Version | Purpose | Status |
|---------|-------------------|---------|--------|
| @astrojs/sitemap | ^3.7.2 | XML sitemap generation | Already in astro.config.mjs integrations array |
| astro | ^5.18.1 | Static site framework | Core framework |
| tailwindcss | ^4.2.2 | Utility-first CSS | All styling |

### No New Dependencies
This phase requires zero npm installs. All work is HTML meta tags, CSS additions, and a static image file.

## Architecture Patterns

### Current Head Structure (Layout.astro)
```
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="..." />
  <meta name="description" content={description} />
  <title>{title}</title>
  <link rel="icon" ... />
  <style is:inline>...</style>
</head>
```

### Target Head Structure
```
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="..." />
  <meta name="description" content={description} />
  <title>{title}</title>
  <link rel="canonical" href={canonicalURL} />
  <link rel="icon" ... />

  <!-- Open Graph -->
  <meta property="og:title" content={title} />
  <meta property="og:description" content={description} />
  <meta property="og:image" content={ogImage} />
  <meta property="og:type" content="website" />
  <meta property="og:url" content={canonicalURL} />

  <!-- Twitter/X Card -->
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:title" content={title} />
  <meta name="twitter:description" content={description} />
  <meta name="twitter:image" content={ogImage} />

  <style is:inline>...</style>
</head>
```

### Pattern: Astro.site for Canonical URLs
Astro provides `Astro.site` which reads from the `site` config in astro.config.mjs. The site is already configured as `https://russellkmoore.me`.

```astro
---
const canonicalURL = new URL(Astro.url.pathname, Astro.site);
const ogImage = new URL("/og-image.png", Astro.site);
---
<link rel="canonical" href={canonicalURL} />
<meta property="og:image" content={ogImage} />
```

This pattern works correctly for static builds -- Astro resolves the full URL at build time.

### Pattern: Skip-to-Content Link
```html
<a href="#main-content" class="skip-link">Skip to content</a>
<!-- ... nav ... -->
<main id="main-content">
```
```css
.skip-link {
  position: absolute;
  top: -100%;
  left: 0;
  z-index: 100;
  padding: 0.5rem 1rem;
  background: var(--color-accent);
  color: white;
}
.skip-link:focus {
  top: 0;
}
```

### Pattern: Focus-Visible Styles (Tailwind v4)
Tailwind v4 includes `focus-visible:` variant by default. For a global approach in global.css:
```css
:focus-visible {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
}
```
This provides consistent violet focus rings across all interactive elements. WCAG AA requires visible focus indicators.

### Current Heading Hierarchy (VERIFIED CORRECT)
```
h1: "Russell Moore" (Hero.astro) -- single h1, correct
  h2: Section titles via SectionHeader.astro (About, Projects, Career, Writing, Let's Connect)
    h3: Individual card titles (project names, company names, post titles)
```
The hierarchy is already properly nested. No changes needed.

### Anti-Patterns to Avoid
- **Adding og:image without absolute URL:** Social crawlers need full `https://` URLs, not relative paths
- **Missing twitter:image:** Some platforms only read Twitter card tags, not OG tags. Include both.
- **Over-engineering OG for a single-page site:** Props interface already has title + description. Just use those same values for OG. Don't create a separate OG metadata system.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Sitemap XML | Manual XML generation | @astrojs/sitemap (already configured) | Handles lastmod, changefreq, proper XML escaping |
| Canonical URL resolution | String concatenation | `new URL(pathname, Astro.site)` | Handles trailing slashes, encoding, edge cases |
| OG image generation | Programmatic image generation | Manual screenshot saved as PNG | User explicitly chose screenshot approach (D-01). No satori/puppeteer needed. |

## Common Pitfalls

### Pitfall 1: OG Image Not Showing on LinkedIn
**What goes wrong:** LinkedIn caches aggressively. Even after adding correct OG tags, sharing the URL shows no preview.
**Why it happens:** LinkedIn caches OG data for up to 7 days. First share without OG tags poisons the cache.
**How to avoid:** After deploying, use LinkedIn Post Inspector (https://www.linkedin.com/post-inspector/) to force a re-scrape. Also works: append a query param like `?v=2` to bust cache during testing.
**Warning signs:** Tags look correct in source but preview doesn't show image.

### Pitfall 2: OG Image Wrong Size or Cropped
**What goes wrong:** Social platforms crop or letterbox the OG image.
**Why it happens:** Not using the standard 1200x630 dimensions, or image has important content near edges.
**How to avoid:** Create image at exactly 1200x630 pixels. Keep key content (name, tagline) in the center 80% of the image. User decision D-01 specifies these dimensions.
**Warning signs:** Image looks fine locally but appears cropped in previews.

### Pitfall 3: Lighthouse Performance Drop from Font Loading
**What goes wrong:** Lighthouse flags font loading as render-blocking or causing layout shift.
**Why it happens:** @fontsource imports add CSS that blocks rendering.
**How to avoid:** The existing `is:inline` style preventing FOUC already handles this. Fonts are bundled by Vite at build time. No additional action likely needed, but verify with Lighthouse.
**Warning signs:** Lighthouse flags "Ensure text remains visible during webfont load" or "Cumulative Layout Shift" issues.

### Pitfall 4: Missing `lang` Attribute or Viewport
**What goes wrong:** Lighthouse accessibility score drops.
**Why it happens:** Missing fundamental HTML attributes.
**How to avoid:** Already handled -- `<html lang="en">` and viewport meta are present. Just verify they survive.
**Warning signs:** Lighthouse accessibility audit flags these.

### Pitfall 5: Mobile Menu Not Keyboard Accessible
**What goes wrong:** Users can't escape the mobile menu with Escape key, or focus gets trapped incorrectly.
**Why it happens:** Mobile menu script uses click events but no keyboard events.
**How to avoid:** Add Escape key handler to close mobile menu. Consider focus trapping within the open menu panel.
**Warning signs:** Tab through the page on mobile viewport -- focus goes behind the open menu overlay.

## Code Examples

### OG Tags in Layout.astro
```astro
---
interface Props {
  title: string;
  description?: string;
}

const { title, description = "Russell Moore -- Engineering Leader & Builder" } = Astro.props;
const canonicalURL = new URL(Astro.url.pathname, Astro.site);
const ogImage = new URL("/og-image.png", Astro.site);
---

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="description" content={description} />
  <title>{title}</title>
  <link rel="canonical" href={canonicalURL} />
  <link rel="icon" type="image/svg+xml" href="/favicon.svg" />

  <!-- Open Graph -->
  <meta property="og:title" content={title} />
  <meta property="og:description" content={description} />
  <meta property="og:image" content={ogImage} />
  <meta property="og:type" content="website" />
  <meta property="og:url" content={canonicalURL} />

  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:title" content={title} />
  <meta name="twitter:description" content={description} />
  <meta name="twitter:image" content={ogImage} />

  <style is:inline>
    html { background-color: #0a0a0f; color: #f5f5f5; }
  </style>
</head>
```

### Hero Animation Speed-Up (D-06)
Current: `animation: meshMove 20s ease-in-out infinite alternate;`
Target: `animation: meshMove 10s ease-in-out infinite alternate;` (midpoint of 8-12s range)

Also increase blob size/opacity per D-06:
```css
.hero-gradient::before {
  /* ... */
  background:
    radial-gradient(circle at 30% 40%, rgba(124, 58, 237, 0.20) 0%, transparent 55%),
    radial-gradient(circle at 70% 60%, rgba(59, 130, 246, 0.12) 0%, transparent 55%),
    radial-gradient(circle at 50% 50%, rgba(124, 58, 237, 0.08) 0%, transparent 70%);
  animation: meshMove 10s ease-in-out infinite alternate;
}
```

### Focus-Visible Global Styles
```css
/* global.css additions */
:focus-visible {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
}

/* Skip-to-content link */
.skip-link {
  position: absolute;
  top: -3rem;
  left: 1rem;
  z-index: 100;
  padding: 0.5rem 1rem;
  background-color: var(--color-accent);
  color: white;
  border-radius: 0.25rem;
  font-size: 0.875rem;
  transition: top 0.2s;
}

.skip-link:focus {
  top: 1rem;
}
```

### Robots.txt
```
User-agent: *
Allow: /

Sitemap: https://russellkmoore.me/sitemap-index.xml
```
Note: @astrojs/sitemap generates `sitemap-index.xml` as the entry point (not `sitemap.xml`). This is the correct reference.

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| og:image with relative URL | Always use absolute URL for og:image | Always been the spec | Relative URLs silently fail on most platforms |
| twitter:card type "summary" | "summary_large_image" for portfolio sites | Standard practice | Large image cards get much higher click-through |
| Separate OG image generation (satori/puppeteer) | Static screenshot for single-page sites | Pragmatic choice | No build-time dependency, user controls the exact image |
| @astrojs/tailwind integration | @tailwindcss/vite direct plugin | Tailwind v4 (2024) | Already using the current approach |

## Open Questions

1. **OG Image Content**
   - What we know: User wants a 1200x630 screenshot of the hero section (D-01)
   - What's unclear: Exact capture method -- browser screenshot + image editor crop, or automated tool
   - Recommendation: Plan should include a manual step: "Take a browser screenshot of the hero at desktop viewport, crop/resize to 1200x630, save as public/og-image.png." This is a human task, not automatable by the executor.

2. **Lighthouse Score Baseline**
   - What we know: Target is 95+ across all four categories
   - What's unclear: Current baseline scores (can't run Lighthouse in this research phase)
   - Recommendation: First task in the plan should run Lighthouse to establish baseline, then optimize from there. The site is static with no images and minimal JS, so scores should already be high.

## Project Constraints (from CLAUDE.md)

- **Tech stack locked:** Astro 5 + TypeScript + Tailwind CSS v4 + static output
- **No @astrojs/tailwind:** Use @tailwindcss/vite directly (already done)
- **No tailwind.config.js:** CSS-based config via @theme (already done)
- **No React/Vue/Svelte:** Astro components only, vanilla JS for interactivity
- **No heavy animations:** CSS transitions only (GSAP, parallax explicitly out of scope)
- **Deployment:** Cloudflare Workers via wrangler
- **Font:** Geist Sans via @fontsource-variable, Inter fallback

## Accessibility Audit (Current State)

### What's Already Good
- `<html lang="en">` present
- Viewport meta tag present
- `aria-label` on menu toggle, close button, footer social links
- `aria-hidden="true"` on decorative hero gradient
- Single `<h1>` in Hero, proper h2/h3 nesting
- Section IDs for anchor navigation
- `rel="noopener noreferrer"` on external links

### What's Missing (Needs Adding)
| Item | Priority | Details |
|------|----------|---------|
| Skip-to-content link | High | No skip link exists. Add before Nav in Layout.astro. |
| Focus-visible styles | High | Zero focus styles anywhere. All interactive elements need visible focus. |
| `<main>` has no ID | Medium | Add `id="main-content"` for skip link target. |
| Mobile menu keyboard | Medium | No Escape key handler. No focus trap when open. |
| Nav landmark role | Low | `<nav>` is semantic, but the outer `<div>` with sentinel could confuse. Low priority. |
| robots.txt | Low | Missing. Add to public/ with sitemap reference. |
| `aria-expanded` on menu toggle | Medium | Menu button should indicate expanded state. |

### What Does NOT Need Changing
- Heading hierarchy -- already correct (h1 > h2 > h3)
- Section structure -- all sections use `<section>` with IDs
- Footer links -- already have aria-labels
- No images to add alt text to (only SVG icons via astro-icon)

## Sources

### Primary (HIGH confidence)
- Codebase audit of all .astro files in src/ -- verified current state of meta tags, ARIA, headings, and structure
- astro.config.mjs -- confirmed site URL, sitemap integration, static output mode
- package.json -- confirmed all dependencies already installed

### Secondary (MEDIUM confidence)
- Open Graph protocol specification (ogp.me) -- standard OG tag names and required properties
- LinkedIn Post Inspector documentation -- cache busting for OG previews
- WCAG 2.1 AA guidelines -- focus visible, skip navigation, heading hierarchy requirements
- @astrojs/sitemap generates sitemap-index.xml as entry point (verified from Astro docs pattern)

### Tertiary (LOW confidence)
- None -- this phase is well-understood HTML/CSS patterns with no ambiguity

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - No new dependencies, everything already installed
- Architecture: HIGH - Pattern is standard HTML meta tags in Astro head, verified against codebase
- Pitfalls: HIGH - OG caching and Lighthouse font issues are well-documented, accessibility gaps identified from direct code audit

**Research date:** 2026-03-26
**Valid until:** 2026-04-26 (stable domain, no fast-moving dependencies)
