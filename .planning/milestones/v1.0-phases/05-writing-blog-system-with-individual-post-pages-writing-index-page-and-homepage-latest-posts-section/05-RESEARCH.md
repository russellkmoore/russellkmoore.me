# Phase 5: Writing/Blog System - Research

**Researched:** 2026-03-27
**Domain:** Astro 5 MDX blog with content collections, Shiki syntax highlighting, Tailwind v4 typography
**Confidence:** HIGH

## Summary

This phase transforms the placeholder "Coming soon" Writing section into a full MDX-powered blog. The core technical pieces are: (1) @astrojs/mdx integration for component-rich posts, (2) Astro 5's `render()` function from `astro:content` for dynamic routes, (3) Shiki dark theme that blends with the site's #0a0a0f background, and (4) @tailwindcss/typography for prose styling in Tailwind v4.

All pieces are well-supported and stable. The content collection already exists with a Zod schema and glob loader -- the main changes are updating the glob pattern to `**/*.{md,mdx}`, adding the MDX integration to astro.config.mjs, and creating the page routes. The BlackMagic reference site confirms these patterns work in production on Astro + Cloudflare.

**Primary recommendation:** Use `@astrojs/mdx@5.0.3`, `@tailwindcss/typography@0.5.19`, and the `github-dark-default` Shiki theme (#0d1117 background -- closest built-in match to the site's #0a0a0f). Override the code block background to `#0a0a0f` via CSS for seamless blending.

<user_constraints>

## User Constraints (from CONTEXT.md)

### Locked Decisions
- **D-01:** Full-width immersive layout -- hero gradient/image spanning full width at top, then centered content column below
- **D-02:** No reading time estimate -- just date and tags
- **D-03:** Post pages at `/writing/[slug]` -- SEO-friendly unique URLs from content collection
- **D-04:** MDX format with @astrojs/mdx integration
- **D-05:** Syntax-highlighted code blocks with dark theme via Astro's built-in Shiki
- **D-06:** Update content collection to support `.mdx` files
- **D-07:** Show latest 3 published posts on homepage, sorted by pubDate descending
- **D-08:** "View all writing" link below the 3 posts, linking to /writing index page
- **D-09:** Post cards are both clickable (entire card links to post) AND have explicit "Read more" text link
- **D-10:** /writing page lists all published posts (draft filtered out), sorted by pubDate descending
- **D-11:** Same card design as homepage but shows all posts
- **D-12:** Writing collection glob pattern updated for .mdx files
- **D-13:** Existing schema fields preserved (title, description, pubDate, tags, draft)

### Claude's Discretion
- Post page hero gradient/image approach (reuse hero gradient pattern or simpler approach)
- Writing index page layout (grid vs list)
- Navigation between posts (previous/next links)
- Tag filtering on index page (if useful)
- Prose typography styles for markdown content
- Back navigation from post page

### Deferred Ideas (OUT OF SCOPE)
- RSS feed (V2-01)
- Reading time estimate (explicitly decided against)
- Tag-based filtering page
- Related posts at bottom of post page
- Social share buttons on posts

</user_constraints>

## Standard Stack

### Core

| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| @astrojs/mdx | 5.0.3 | MDX support in Astro | Official Astro integration. Enables JSX components in markdown, works with content collections. Required for D-04. |
| @tailwindcss/typography | 0.5.19 | Prose styling for rendered markdown | Official Tailwind plugin. Provides `prose` classes for headings, paragraphs, lists, code blocks. Compatible with Tailwind v4 via `@plugin` directive. |

### Configuration

No new supporting libraries needed. Shiki is bundled with Astro. Content collections already configured.

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| @tailwindcss/typography | Hand-rolled prose CSS | Typography plugin handles 50+ edge cases (nested lists, code in headings, blockquote styling). Not worth hand-rolling. |
| @astrojs/mdx | Plain .md files | User explicitly chose MDX for component embedding (callout boxes, interactive elements). Decision D-04 is locked. |

**Installation:**
```bash
npm install @astrojs/mdx @tailwindcss/typography
```

## Architecture Patterns

### New Files Structure
```
src/
├── pages/
│   ├── writing/
│   │   ├── index.astro        # Writing index page (D-10, D-11)
│   │   └── [...slug].astro    # Individual post pages (D-03)
├── layouts/
│   └── PostLayout.astro       # Post-specific layout wrapper (D-01)
├── components/
│   └── PostCard.astro         # Reusable post card (D-09, shared by homepage + index)
```

### Pattern 1: Dynamic Routes with Content Collections (Astro 5)

**What:** Astro 5 changed how content collection entries are rendered. The `render()` function is now imported from `astro:content` (not called on the entry object).

**When to use:** Every `[slug].astro` page that renders collection content.

**Critical Astro 5 change:** In Astro 5, content collection entries use `id` not `slug`. The `slug` field no longer exists as a reserved property. Use `post.id` for routing.

**Example:**
```astro
---
// src/pages/writing/[...slug].astro
import { getCollection, render } from 'astro:content';
import PostLayout from '../../layouts/PostLayout.astro';

export async function getStaticPaths() {
  const posts = await getCollection('writing', ({ data }) => !data.draft);
  return posts.map((post) => ({
    params: { slug: post.id },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content, headings } = await render(post);
---

<PostLayout frontmatter={post.data}>
  <Content />
</PostLayout>
```
Source: [Astro Content Collections docs](https://docs.astro.build/en/guides/content-collections/), [Astro v5 upgrade guide](https://docs.astro.build/en/guides/upgrade-to/v5/)

### Pattern 2: MDX Integration Setup

**What:** Add @astrojs/mdx to Astro config and update content collection glob.

**Example:**
```javascript
// astro.config.mjs
import mdx from '@astrojs/mdx';

export default defineConfig({
  site: "https://russellkmoore.me",
  output: "static",
  integrations: [mdx(), sitemap(), icon()],
  markdown: {
    shikiConfig: {
      theme: 'github-dark-default',
    },
  },
  vite: {
    plugins: [tailwindcss()],
  },
});
```

```typescript
// src/content.config.ts -- update glob pattern
const writing = defineCollection({
  loader: glob({ pattern: "**/*.{md,mdx}", base: "./src/content/writing" }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    pubDate: z.coerce.date(),
    tags: z.array(z.string()).default([]),
    draft: z.boolean().default(true),
  }),
});
```
Source: [Astro MDX integration docs](https://docs.astro.build/en/guides/integrations-guide/mdx/)

### Pattern 3: Tailwind Typography v4 Setup

**What:** Use `@plugin` directive (not tailwind.config.js) for Tailwind v4.

**Example:**
```css
/* src/styles/global.css */
@import "tailwindcss";
@plugin "@tailwindcss/typography";

/* ... existing theme tokens ... */
```

Apply with `prose` and `dark:prose-invert` classes:
```html
<article class="prose prose-invert prose-lg max-w-none">
  <Content />
</article>
```

**Dark theme customization:** Use CSS custom properties to override prose colors:
```css
/* Custom prose colors for the dark theme */
.prose {
  --tw-prose-body: #e4e4e7;
  --tw-prose-headings: #f5f5f5;
  --tw-prose-links: #a78bfa;
  --tw-prose-bold: #f5f5f5;
  --tw-prose-code: #e4e4e7;
  --tw-prose-quotes: #a1a1aa;
  --tw-prose-quote-borders: #7c3aed;
  --tw-prose-pre-bg: #0a0a0f;
  --tw-prose-pre-code: #e4e4e7;
}
```
Source: [tailwindcss-typography GitHub](https://github.com/tailwindlabs/tailwindcss-typography)

### Pattern 4: Per-Post OG Meta Tags

**What:** Override Layout.astro's default OG tags with post-specific frontmatter.

**Example:**
```astro
---
// PostLayout.astro
import Layout from './Layout.astro';

interface Props {
  frontmatter: {
    title: string;
    description: string;
    pubDate: Date;
    tags: string[];
  };
}

const { frontmatter } = Astro.props;
---

<Layout
  title={`${frontmatter.title} | Russell Moore`}
  description={frontmatter.description}
>
  <slot />
</Layout>
```

**Note:** Layout.astro already accepts `title` and `description` props and uses them for OG tags. Post pages just pass frontmatter values through. The `og:type` should be overridden to `"article"` for post pages -- this requires adding an `ogType` prop to Layout.astro.

### Anti-Patterns to Avoid
- **Using `post.slug` instead of `post.id`:** Astro 5 content layer entries do not have a `slug` field. Use `post.id` which comes from the filename minus extension.
- **Calling `post.render()` as a method:** Astro 5 changed this. Import `render` from `astro:content` and call `render(post)`.
- **Using `@import "@tailwindcss/typography"` instead of `@plugin`:** The typography plugin must use `@plugin` directive in Tailwind v4, not `@import`.
- **Adding tailwind.config.js for typography:** Tailwind v4 uses CSS-based config. The typography plugin works via `@plugin` in CSS.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Prose typography | Custom CSS for headings, lists, blockquotes, code blocks in rendered markdown | @tailwindcss/typography `prose` classes | 50+ edge cases: nested lists, code in headings, table styling, blockquote nesting, link styling |
| Syntax highlighting | Custom code block component with manual highlighting | Astro's built-in Shiki (zero config) | Shiki handles 200+ languages, line numbers, line highlighting. Already bundled. |
| MDX compilation | Custom markdown-to-HTML pipeline | @astrojs/mdx integration | Handles import resolution, component rendering, frontmatter extraction, Vite HMR |
| Date formatting | Custom date parsing/formatting | `Date.toLocaleDateString()` | Built-in, handles locale-aware formatting |

## Common Pitfalls

### Pitfall 1: Astro 5 render() API Change
**What goes wrong:** Using `const { Content } = await post.render()` fails with "render is not a function"
**Why it happens:** Astro 5 changed content entries to plain serializable objects. The render() method was moved to a standalone import.
**How to avoid:** `import { render } from 'astro:content'` then `const { Content } = await render(post)`
**Warning signs:** TypeScript error on `.render()` call, runtime crash

### Pitfall 2: Using slug Instead of id
**What goes wrong:** `post.slug` is undefined, routes don't generate
**Why it happens:** Astro 5 content layer removed the reserved `slug` field. Entries now use `id` (derived from filename).
**How to avoid:** Use `post.id` in getStaticPaths params. For URL-friendly slugs, `post.id` already strips the file extension.
**Warning signs:** Empty params, 404 pages, build warnings

### Pitfall 3: MDX Files Not Found by Glob Loader
**What goes wrong:** MDX files in src/content/writing/ don't appear in collection
**Why it happens:** Current glob pattern is `"**/*.md"` which excludes `.mdx` files
**How to avoid:** Update to `"**/*.{md,mdx}"` in content.config.ts
**Warning signs:** MDX posts missing from getCollection() results

### Pitfall 4: Code Block Background Mismatch
**What goes wrong:** Shiki-highlighted code blocks have a noticeably different background from the page
**Why it happens:** Most dark themes have backgrounds like #1a1b26 or #121212, not matching #0a0a0f
**How to avoid:** Set `--tw-prose-pre-bg: #0a0a0f` in CSS overrides, or use Shiki's CSS variables theme mode
**Warning signs:** Visible color discontinuity around code blocks

### Pitfall 5: Typography Plugin Not Loading in Tailwind v4
**What goes wrong:** `prose` classes have no effect
**Why it happens:** Using `@import` instead of `@plugin` directive, or adding to a nonexistent tailwind.config.js
**How to avoid:** Add `@plugin "@tailwindcss/typography"` in global.css after `@import "tailwindcss"`
**Warning signs:** No prose styles applied, raw unstyled markdown output

### Pitfall 6: Rest/Spread Route vs Named Route
**What goes wrong:** Posts at nested paths like `/writing/subdir/post` generate unexpected URLs
**Why it happens:** `[...slug].astro` captures all segments, `[slug].astro` captures one segment
**How to avoid:** Use `[...slug].astro` to handle potential nested content paths from glob loader, even if current content is flat
**Warning signs:** 404 for posts with nested ids

## Code Examples

### Complete Post Page Route
```astro
---
// src/pages/writing/[...slug].astro
import { getCollection, render } from 'astro:content';
import PostLayout from '../../layouts/PostLayout.astro';

export async function getStaticPaths() {
  const posts = await getCollection('writing', ({ data }) => !data.draft);
  return posts.map((post) => ({
    params: { slug: post.id },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content } = await render(post);

function formatDate(date: Date): string {
  return date.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  });
}
---

<PostLayout
  title={post.data.title}
  description={post.data.description}
>
  <header class="mb-12">
    <time class="text-sm text-muted">{formatDate(post.data.pubDate)}</time>
    <h1 class="mt-2 text-4xl font-bold text-foreground">{post.data.title}</h1>
    <div class="mt-4 flex gap-2">
      {post.data.tags.map((tag) => (
        <span class="text-xs text-accent">{tag}</span>
      ))}
    </div>
  </header>
  <article class="prose prose-invert prose-lg max-w-none">
    <Content />
  </article>
</PostLayout>
```
Source: Astro 5 content collections docs + MDX integration docs

### Homepage Latest 3 Posts
```astro
---
import { getCollection } from 'astro:content';

const publishedWriting = await getCollection('writing', ({ data }) => !data.draft);
const latestPosts = publishedWriting
  .sort((a, b) => b.data.pubDate.getTime() - a.data.pubDate.getTime())
  .slice(0, 3);
---

{latestPosts.length > 0 ? (
  <div class="grid gap-6 md:grid-cols-3">
    {latestPosts.map((post) => (
      <a href={`/writing/${post.id}`} class="group block">
        <!-- PostCard content -->
      </a>
    ))}
  </div>
  <a href="/writing" class="mt-8 inline-flex items-center text-accent hover:text-accent-hover">
    View all writing &rarr;
  </a>
) : (
  <p class="text-lg text-muted">Coming soon.</p>
)}
```

## Shiki Theme Analysis

Investigated all bundled Shiki dark themes for background color compatibility with site bg (#0a0a0f):

| Theme | Background | Delta from #0a0a0f | Notes |
|-------|------------|-------------------|-------|
| vitesse-black | #000000 | Too dark | Pure black, stark |
| aurora-x | #07090F | Very close | Near-identical to #0a0a0f |
| ayu-dark | #0d1017 | Close | Slightly lighter, blue undertone |
| github-dark-default | #0d1117 | Close | GitHub's dark default, well-maintained |
| vesper | #101010 | Close | Neutral dark, no color tint |
| material-theme-ocean | #0F111A | Close | Blue undertone |
| vitesse-dark | #121212 | Moderate | Neutral gray |
| tokyo-night | #1a1b26 | Noticeable | Blue-purple tint, popular |
| houston | #17191e | Noticeable | Astro's official theme |

**Recommendation:** Use `github-dark-default` (#0d1117) as the Shiki theme. It is close to #0a0a0f, well-maintained by GitHub, has excellent language coverage, and professional aesthetic matching the linear.app energy. Override `--tw-prose-pre-bg: #0a0a0f` in CSS so the code block container matches the page background exactly while Shiki token colors remain from the github-dark-default palette.

Alternative: `aurora-x` (#07090F) is the closest color match but less widely used. If exact blending is critical and the tokenization colors look good, it's viable.

## Discretion Recommendations

For items marked as Claude's discretion in CONTEXT.md:

### Post Page Hero Approach
**Recommend:** Simple gradient fade using existing accent color, not a reuse of the Hero.astro gradient. A subtle top gradient that fades from a slightly tinted color to the background (#0a0a0f) gives the "immersive" feel without adding complexity. No image required for v1.

### Writing Index Layout
**Recommend:** Vertical list layout (not grid). For a thought-leadership blog, a single-column list with larger titles and visible descriptions reads more like a professional publication. Grid works for visual content (thumbnails), but text-heavy posts benefit from list format. The homepage section can use a 3-column grid for compact display.

### Previous/Next Navigation
**Recommend:** Include simple prev/next links at the bottom of post pages. Minimal effort, good UX for readers who want to continue. Sort by pubDate same as the index.

### Tag Filtering
**Recommend:** Skip for now. Only 3 posts planned. Tags display on cards for context but no filtering UI. Consistent with deferred ideas list.

### Back Navigation
**Recommend:** Add a "Back to Writing" link above the post title. Simple anchor link to /writing.

### Prose Typography
**Recommend:** Use `prose-invert` with custom CSS variable overrides (documented in Architecture Patterns above) to match the site's color scheme. Override link color to accent (#a78bfa), quote borders to violet, code backgrounds to #0a0a0f.

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| `post.render()` method | `render(post)` standalone import from `astro:content` | Astro 5.0 (Dec 2024) | Must import render function, entries are plain objects |
| `post.slug` field | `post.id` field | Astro 5.0 (Dec 2024) | No reserved slug; id comes from filename |
| `tailwind.config.js` plugins | `@plugin` directive in CSS | Tailwind v4.0 (Jan 2025) | Typography plugin loaded via CSS, not JS config |
| `@astrojs/tailwind` integration | `@tailwindcss/vite` direct plugin | Astro 5.2 / Tailwind v4 | Already configured in this project |

## Open Questions

1. **Content file extension migration**
   - What we know: Existing 3 articles are `.md` files with placeholder content
   - What's unclear: Whether they should be renamed to `.mdx` now or kept as `.md` until they need MDX features
   - Recommendation: Keep as `.md` for now. The updated glob pattern (`**/*.{md,mdx}`) handles both. Rename to `.mdx` only when adding JSX components to a specific post.

2. **Draft articles for development**
   - What we know: All 3 existing articles have `draft: true`
   - What's unclear: Should at least one be set to `draft: false` for development/testing?
   - Recommendation: Set one post to `draft: false` during development so the blog system can be visually verified. Revert if needed before deploy.

## Project Constraints (from CLAUDE.md)

- **Tech stack:** Astro + TypeScript + Tailwind CSS + @astrojs/cloudflare -- locked
- **Output mode:** Static (`output: "static"`) -- all pages pre-rendered at build
- **Font:** Geist Sans via @fontsource -- applies to prose content too
- **Design:** Dark theme with violet accent, linear.app/vercel.com energy
- **No React/Vue/Svelte:** Pure .astro components with vanilla JS for any interactivity
- **Avoid:** @astrojs/tailwind (deprecated), tailwind.config.js (v3 legacy), postcss.config.js (built-in)
- **Tailwind v4:** CSS-based config with @theme tokens, @plugin for plugins

## Sources

### Primary (HIGH confidence)
- [Astro MDX Integration docs](https://docs.astro.build/en/guides/integrations-guide/mdx/) - install, config, content collection rendering
- [Astro Content Collections docs](https://docs.astro.build/en/guides/content-collections/) - getStaticPaths, render() API
- [Astro Syntax Highlighting docs](https://docs.astro.build/en/guides/syntax-highlighting/) - Shiki config in astro.config.mjs
- [Astro v5 Upgrade Guide](https://docs.astro.build/en/guides/upgrade-to/v5/) - render() and id changes
- [tailwindcss-typography GitHub](https://github.com/tailwindlabs/tailwindcss-typography) - v4 @plugin setup, prose-invert
- Shiki bundled themes (verified via local `shiki` package) - background colors for 17 dark themes
- BlackMagic reference site (`~/Workspaces/justblackmagic/`) - production MDX blog patterns

### Secondary (MEDIUM confidence)
- npm registry versions: @astrojs/mdx@5.0.3, @tailwindcss/typography@0.5.19

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - official Astro integration, verified npm versions, confirmed Tailwind v4 compatibility
- Architecture: HIGH - Astro 5 patterns verified against official docs and upgrade guide; BlackMagic reference confirms production viability
- Pitfalls: HIGH - Astro 5 API changes well-documented; Shiki theme backgrounds empirically verified
- Shiki theme: HIGH - background colors extracted directly from bundled theme files

**Research date:** 2026-03-27
**Valid until:** 2026-04-27 (stable ecosystem, Astro 5 mature)
