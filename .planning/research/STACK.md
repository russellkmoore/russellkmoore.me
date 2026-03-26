# Stack Research

**Domain:** Personal portfolio site (static, content-driven)
**Researched:** 2026-03-26
**Confidence:** HIGH

## Recommended Stack

### Core Technologies

| Technology | Version | Purpose | Why Recommended |
|------------|---------|---------|-----------------|
| Astro | ^5.18.0 | Static site framework | Latest stable v5 release. Content collections with Zod schemas, built-in Vite, static output mode. Astro 6 is in beta -- stick with v5 for production stability. Acquired by Cloudflare (Jan 2026), so Cloudflare integration is first-class. |
| TypeScript | ^5.7.0 | Type safety | Bundled with Astro, configured via tsconfig.json. Astro provides built-in TS support with zero config. |
| Tailwind CSS | ^4.2.2 | Utility-first styling | v4 is a ground-up rewrite: 5x faster full builds, 100x faster incremental builds. CSS-first config (no tailwind.config.js). Uses `@import "tailwindcss"` instead of directives. |
| @tailwindcss/vite | ^4.2.2 | Tailwind Vite integration | Replaces deprecated @astrojs/tailwind. Direct Vite plugin added to Astro config. This is the official way to use Tailwind v4 with Astro 5.2+. |
| @astrojs/cloudflare | ^12.6.12 | Cloudflare Workers adapter | Latest stable for Astro 5. Set `output: "static"` in Astro config. v13 is for Astro 6 beta only. |
| Wrangler | ^4.77.0 | Cloudflare deployment CLI | v4 is current stable. Deploys static assets to Cloudflare Workers. Node.js 18+ required. |

### Supporting Libraries

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| @fontsource-variable/geist | ^1.0.0 | Geist Sans variable font | Import in Layout.astro. Variable font = single file for all weights (100-900). Smaller than static per-weight files. |
| @fontsource/inter | ^5.2.5 | Inter fallback font | CSS fallback only. Import 400/500/600/700 weights. Renders if Geist fails to load. |
| @astrojs/sitemap | ^3.7.1 | XML sitemap generation | Add to integrations array. Auto-generates sitemap.xml at build. Set `site` in astro.config.mjs. |
| astro-icon | ^1.1.5 | SVG icon component | For LinkedIn, GitHub, and other icons in nav/footer. Wraps Iconify with 275K+ icons. Use `@iconify-json/mdi` or `@iconify-json/lucide` icon set. |
| @iconify-json/lucide | latest | Lucide icon set | Clean, minimal icons that match the linear.app aesthetic. Used with astro-icon. |
| sharp | (bundled) | Build-time image optimization | Bundled with Astro. Works at build time for static output. No runtime image processing on Cloudflare Workers. |

### Development Tools

| Tool | Purpose | Notes |
|------|---------|-------|
| prettier | Code formatting | Use with `prettier-plugin-astro` and `prettier-plugin-tailwindcss` for .astro file and class sorting support. |
| prettier-plugin-astro | Astro file formatting | Formats .astro component files. |
| prettier-plugin-tailwindcss | Tailwind class sorting | Auto-sorts Tailwind classes in templates. Must be loaded last in Prettier plugins array. |
| typescript | Type checking | Astro bundles TS compilation, but install explicitly for IDE support and `astro check`. |

## Installation

```bash
# Create project
npm create astro@latest

# Core dependencies
npm install astro@latest @astrojs/cloudflare@^12.6.12 tailwindcss@^4.2.2 @tailwindcss/vite@^4.2.2

# Fonts
npm install @fontsource-variable/geist @fontsource/inter

# Supporting
npm install @astrojs/sitemap astro-icon @iconify-json/lucide

# Dev dependencies
npm install -D wrangler@^4.77.0 prettier prettier-plugin-astro prettier-plugin-tailwindcss typescript
```

## Key Configuration Details

### Tailwind CSS v4 Setup (NOT @astrojs/tailwind)

```javascript
// astro.config.mjs
import { defineConfig } from "astro/config";
import cloudflare from "@astrojs/cloudflare";
import tailwindcss from "@tailwindcss/vite";
import sitemap from "@astrojs/sitemap";
import icon from "astro-icon";

export default defineConfig({
  site: "https://russellkmoore.me",
  output: "static",
  adapter: cloudflare(),
  integrations: [sitemap(), icon()],
  vite: {
    plugins: [tailwindcss()],
  },
});
```

```css
/* src/styles/global.css */
@import "tailwindcss";

/* Tailwind v4: CSS-based config, no tailwind.config.js needed */
@theme {
  --color-background: #0a0a0f;
  --color-foreground: #f5f5f5;
  --color-muted: #a1a1aa;
  --color-accent: #7c3aed;
  --font-sans: "Geist Variable", "Inter", system-ui, sans-serif;
}
```

### Font Setup in Layout.astro

```astro
---
import "@fontsource-variable/geist";
import "@fontsource/inter/400.css";
import "@fontsource/inter/500.css";
import "@fontsource/inter/600.css";
import "@fontsource/inter/700.css";
import "../styles/global.css";
---
```

### Content Collections (src/content.config.ts)

```typescript
import { defineCollection, z } from "astro:content";
import { glob } from "astro/loaders";

const projects = defineCollection({
  loader: glob({ pattern: "**/*.md", base: "./src/content/projects" }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    tags: z.array(z.string()),
    status: z.enum(["Live", "In Development"]),
    url: z.string().url().optional(),
    order: z.number().default(0),
  }),
});

const writing = defineCollection({
  loader: glob({ pattern: "**/*.md", base: "./src/content/writing" }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    pubDate: z.coerce.date(),
    draft: z.boolean().default(true),
  }),
});

export const collections = { projects, writing };
```

### Image Service for Static + Cloudflare

```javascript
// In astro.config.mjs, add:
image: {
  service: {
    entrypoint: "astro/assets/services/sharp",
  },
},
```

Sharp works at build time for static output. Images are optimized during `astro build`, not at runtime. This is fine for a portfolio site where all images are known at build time.

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| @astrojs/tailwind | Deprecated for Tailwind v4. Only exists as migration convenience. | @tailwindcss/vite plugin directly in Astro's vite config |
| tailwind.config.js | Tailwind v4 uses CSS-based configuration via @theme directive. JS config is v3 legacy. | @theme block in global.css |
| postcss.config.js | Tailwind v4 has PostCSS and autoprefixer built-in. No separate config needed. | Nothing -- it is automatic |
| Astro 6 (beta) | Released Jan 2026, still beta. Breaking changes with adapter compatibility (requires @astrojs/cloudflare v13). | Astro 5.18 (stable) |
| @astrojs/cloudflare v13 | Beta-only, requires Astro 6. Not production-ready. | @astrojs/cloudflare v12.6.12 |
| Squoosh image service | Library abandoned, removed from Astro. | Sharp (default, bundled) |
| @fontsource/geist-sans (static) | Static font files = separate file per weight. Larger total download. | @fontsource-variable/geist (single variable font file) |
| React/Vue/Svelte | Zero client-side interactivity needed for this portfolio. Adding a framework adds JS bundle weight for no benefit. | Astro components (.astro files) with vanilla JS for animations |
| Cloudflare Pages | Workers is the modern deployment target. Pages is being unified into Workers. Astro+Cloudflare docs recommend Workers. | Cloudflare Workers via wrangler |

## Version Compatibility

| Package | Compatible With | Notes |
|---------|-----------------|-------|
| astro@5.18.x | @astrojs/cloudflare@12.x | Stable pairing. Do not mix Astro 5 with cloudflare adapter v13. |
| astro@5.18.x | tailwindcss@4.x + @tailwindcss/vite@4.x | Supported since Astro 5.2. Use vite plugin, not @astrojs/tailwind. |
| tailwindcss@4.x | @tailwindcss/vite@4.x | Versions must match (both 4.2.x). They are released in lockstep. |
| wrangler@4.x | Node.js 18+ | Wrangler v4 dropped Node 16 support. |
| @astrojs/sitemap@3.x | astro@5.x | Requires `site` to be set in astro.config.mjs. |
| astro-icon@1.x | astro@4.x / 5.x | v1 has breaking changes from v0.x. Use v1 API. |

## Static Output Note

With `output: "static"`, the @astrojs/cloudflare adapter pre-renders all pages at build time. There is no SSR, no server-side logic, no Cloudflare Workers runtime code. The adapter simply configures the build output for Cloudflare's static asset serving infrastructure. This is the simplest and fastest deployment model -- ideal for a portfolio site.

Technically, for purely static output you could skip the adapter entirely and deploy with `wrangler deploy` against the `dist/` folder. However, the adapter ensures correct output structure and is the documented approach.

## Sources

- [Astro npm](https://www.npmjs.com/package/astro) -- v5.18.0 confirmed latest stable
- [Astro 6 Beta announcement](https://astro.build/blog/astro-6-beta/) -- confirmed beta status, not production-ready
- [@astrojs/cloudflare npm](https://www.npmjs.com/package/@astrojs/cloudflare) -- v12.6.12 confirmed latest for Astro 5
- [Tailwind CSS v4.0 release](https://tailwindcss.com/blog/tailwindcss-v4) -- CSS-first config, Vite plugin approach
- [@tailwindcss/vite npm](https://www.npmjs.com/package/@tailwindcss/vite) -- v4.2.2 confirmed
- [Astro Tailwind docs](https://docs.astro.build/en/guides/integrations-guide/tailwind/) -- confirms @astrojs/tailwind deprecated for v4
- [Astro 5.2 release](https://astro.build/blog/astro-520/) -- native @tailwindcss/vite support
- [Wrangler npm](https://www.npmjs.com/package/wrangler) -- v4.77.0 confirmed
- [Wrangler v4 changelog](https://developers.cloudflare.com/changelog/post/2025-03-13-wrangler-v4/) -- v4 features
- [Astro Content Collections docs](https://docs.astro.build/en/guides/content-collections/) -- Zod schema validation
- [@fontsource-variable/geist npm](https://www.npmjs.com/package/@fontsource-variable/geist) -- variable font package
- [@astrojs/sitemap npm](https://www.npmjs.com/package/@astrojs/sitemap) -- v3.7.1 confirmed
- [astro-icon docs](https://www.astroicon.dev/) -- v1.1.5 confirmed
- [Cloudflare acquires Astro](https://astro.build/blog/year-in-review-2025/) -- Jan 2026 acquisition context

---
*Stack research for: russellkmoore.me personal portfolio*
*Researched: 2026-03-26*
