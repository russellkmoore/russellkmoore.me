# Phase 1: Foundation & Scaffolding - Research

**Researched:** 2026-03-26
**Domain:** Astro 5 static site scaffolding with Tailwind v4, Cloudflare Workers deployment, content collections
**Confidence:** HIGH

## Summary

Phase 1 creates a greenfield Astro 5 project from an empty repository. The core deliverables are: a working build pipeline (dev/build/deploy), dark-themed page with correct fonts (no FOUC, no CLS), content collections with seed data, and Cloudflare Workers deployment. All technology choices are locked in CLAUDE.md and CONTEXT.md -- no stack decisions remain open.

One critical conflict exists between the user's decision D-01 (use `@astrojs/cloudflare` adapter from day one) and the official Astro documentation, which states the adapter is for on-demand rendering only and does not support `output: "static"`. The research-backed recommendation is to skip the adapter for Phase 1 and add it only when switching to server/hybrid output. This is documented below so the planner can address it.

**Primary recommendation:** Initialize with `npm create astro@latest`, install exact locked versions, configure Tailwind v4 via `@tailwindcss/vite` plugin, deploy static output to Cloudflare Workers via wrangler without the adapter.

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- **D-01:** Use `@astrojs/cloudflare` adapter from day one, even though v1 is static content -- keeps SSR path ready for future dynamic features (contact form, auth, API routes)
- **D-02:** Configure `wrangler.toml` for russellkmoore.me domain with Workers deployment
- **D-03:** `output: "static"` in Astro config for now; easy to switch to `"server"` or `"hybrid"` later when SSR features are needed
- **D-04:** Lock to Astro 5.x stable (5.18+). Do NOT use Astro 6 beta -- adapter compatibility issues
- **D-05:** Content collections config at `src/content.config.ts` (Astro 5+ location, not legacy `src/content/config.ts`)
- **D-06:** Tailwind CSS v4 with `@tailwindcss/vite` plugin -- CSS-based config using `@theme` directive, NOT JS tailwind.config.js
- **D-07:** Hardcode `class="dark"` on `<html>` element -- no toggle, dark is the brand
- **D-08:** Inline `<style>` in `<head>` setting background color to prevent FOUC (white flash on load)
- **D-09:** Theme tokens: background ~#0a0a0f, accent #7c3aed (lighter on hover), off-white primary text, muted gray secondary
- **D-10:** Use `@fontsource-variable/geist` (variable font, single file covers all weights 100-900)
- **D-11:** Inter as CSS fallback font
- **D-12:** Preload font file to prevent layout shift (CLS)
- **D-13:** Project status field: enum with 3 values -- "Live", "In Development", "Archived"
- **D-14:** Project featured field: boolean -- featured projects sort first in display
- **D-15:** Project stack field: string array (free-form)
- **D-16:** Project url field: optional string
- **D-17:** Writing tags field: free-form string array
- **D-18:** Writing draft field: boolean -- draft:true entries never render
- **D-19:** Two seed projects: Mercora (AI e-commerce, In Development) and RecompAI (AI coaching, In Development)
- **D-20:** Three seed writing entries (all draft:true)

### Claude's Discretion
- Exact wrangler.toml configuration details (asset serving, routing)
- npm script implementation (dev/build/deploy commands)
- README structure and content depth
- TypeScript configuration (strict mode, path aliases)
- Exact Tailwind v4 theme token syntax

### Deferred Ideas (OUT OF SCOPE)
None -- discussion stayed within phase scope
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| INFRA-01 | Site builds with Astro 5 + TypeScript and deploys to Cloudflare Workers via wrangler | Standard Stack section covers exact versions; Architecture Patterns section covers wrangler config; Adapter Conflict section addresses deployment approach |
| INFRA-02 | Tailwind CSS v4 configured with dark theme tokens | Code Examples: Tailwind v4 theme setup with @custom-variant dark and @theme tokens |
| INFRA-03 | Geist Sans font loaded via @fontsource with Inter fallback, no visible layout shift | Code Examples: Font loading pattern with preload; font file is `geist-latin-wght-normal.woff2` |
| INFRA-04 | Content collections defined for projects/ and writing/ with typed schemas | Code Examples: Content collection config with glob loader at src/content.config.ts |
| INFRA-05 | Seed project entries: Mercora and RecompAI | Schema fields defined in D-13 through D-16; glob loader reads from src/content/projects/ |
| INFRA-06 | Seed writing entries (all draft:true): 3 articles | Schema fields defined in D-17/D-18; glob loader reads from src/content/writing/ |
| INFRA-07 | npm scripts work: dev, build, deploy | Architecture Patterns: npm script definitions |
| INFRA-08 | README documents local dev setup and deploy instructions | Claude's Discretion area; structure recommendation in Architecture Patterns |
</phase_requirements>

## Critical Conflict: Adapter + Static Output

**Decision D-01 conflicts with D-03 and official Astro documentation.**

The user locked D-01: "Use `@astrojs/cloudflare` adapter from day one." They also locked D-03: "`output: 'static'`." However, official Astro docs state:

> "This adapter allows Astro to deploy your **on-demand rendered** routes and features to Cloudflare."
> "If you're using Astro as a static site builder, you **don't need an adapter**."

The `@astrojs/cloudflare` adapter defaults to `output: 'server'`. Combining it with `output: 'static'` caused a deployment bug in Astro v6 (issue #15650 -- the adapter deleted the server directory that wrangler config pointed to). While that was an Astro 6 issue, the fundamental design is that the adapter exists for SSR, not static.

**Recommended resolution for planner:**
1. Skip the adapter for Phase 1 -- deploy static `dist/` directly via wrangler
2. The "SSR-ready" goal from D-01 is achieved by simply running `npm install @astrojs/cloudflare` and updating `astro.config.mjs` when switching to `output: "server"` later -- it is a 2-minute change
3. Flag this to the user as a research-informed deviation from D-01

**If the planner must honor D-01 literally:** Install the adapter but set `output: "server"` (the adapter's expected mode). Then add `export const prerender = true` to every page to get the same effect as static output. This is over-engineered for a site with one page but technically satisfies both D-01 and the adapter's design.

## Standard Stack

### Core (Verified 2026-03-26)

| Library | Version | Purpose | Registry Verified |
|---------|---------|---------|-------------------|
| astro | 5.18.1 | Static site framework | Yes -- latest 5.x (6.1.1 is latest overall, but D-04 locks to 5.x) |
| typescript | ^5.7.0 | Type safety | Bundled with Astro |
| tailwindcss | 4.2.2 | Utility-first CSS | Yes -- current stable |
| @tailwindcss/vite | 4.2.2 | Tailwind Vite plugin | Yes -- must match tailwindcss version |
| wrangler | ^4.77.0 | Cloudflare deploy CLI | Yes -- current stable (dev dependency) |

### Supporting (Verified 2026-03-26)

| Library | Version | Purpose |
|---------|---------|---------|
| @fontsource-variable/geist | 5.2.8 | Geist Sans variable font (single file, all weights) |
| @fontsource/inter | 5.2.8 | Inter fallback font |
| @astrojs/sitemap | 3.7.2 | XML sitemap generation |

### Dev Tools (Verified 2026-03-26)

| Library | Version | Purpose |
|---------|---------|---------|
| prettier | 3.8.1 | Code formatting |
| prettier-plugin-astro | 0.14.1 | .astro file formatting |
| prettier-plugin-tailwindcss | 0.7.2 | Tailwind class sorting (must be last in plugins array) |

### NOT Installed in Phase 1

| Library | Why Skipped | When to Add |
|---------|-------------|-------------|
| @astrojs/cloudflare | Not needed for static output (see Conflict section) | When switching to server/hybrid output |
| astro-icon | No icons needed until nav/footer in Phase 2 | Phase 2 |
| @iconify-json/lucide | Paired with astro-icon | Phase 2 |

### Alternatives Considered

None -- all stack decisions are locked in CLAUDE.md.

**Installation:**
```bash
# Initialize Astro project
npm create astro@latest -- --template minimal --typescript strict

# Core dependencies
npm install astro@^5.18.1 tailwindcss@^4.2.2 @tailwindcss/vite@^4.2.2

# Fonts
npm install @fontsource-variable/geist @fontsource/inter

# Sitemap (for site config)
npm install @astrojs/sitemap

# Dev dependencies
npm install -D wrangler@^4.77.0 prettier prettier-plugin-astro prettier-plugin-tailwindcss typescript
```

## Architecture Patterns

### Phase 1 Project Structure

```
russellkmoore.me/
├── public/                        # Static assets (copied as-is)
│   └── favicon.svg
├── src/
│   ├── content/                   # Content collection source files
│   │   ├── projects/
│   │   │   ├── mercora.md
│   │   │   └── recompai.md
│   │   └── writing/
│   │       ├── executives-who-ship-code.md
│   │       ├── mcp-integration-pattern.md
│   │       └── composable-commerce-ai.md
│   ├── layouts/
│   │   └── Layout.astro           # HTML shell: head, dark theme, fonts, slot
│   ├── pages/
│   │   └── index.astro            # Minimal page proving collections work
│   └── styles/
│       └── global.css             # Tailwind import, @custom-variant dark, @theme tokens
├── src/content.config.ts          # Collection schemas (Astro 5+ location)
├── astro.config.mjs               # Astro + Tailwind vite plugin + sitemap
├── tsconfig.json                  # TypeScript config
├── wrangler.toml                  # Cloudflare Workers static asset config
├── .prettierrc                    # Prettier config with plugins
└── package.json                   # Scripts: dev, build, deploy
```

**Key structural decisions:**
- `src/content.config.ts` at `src/` root, NOT `src/content/config.ts` (D-05)
- No `tailwind.config.js` -- CSS-based config only (D-06)
- No `postcss.config.js` -- Tailwind v4 handles PostCSS internally
- `wrangler.toml` per D-02 (not .jsonc -- both work, .toml is the user's decision)
- No `src/components/` or `src/sections/` yet -- those are Phase 2

### Pattern 1: Astro Config with Tailwind v4 Vite Plugin

```javascript
// astro.config.mjs
import { defineConfig } from "astro/config";
import tailwindcss from "@tailwindcss/vite";
import sitemap from "@astrojs/sitemap";

export default defineConfig({
  site: "https://russellkmoore.me",
  output: "static",
  integrations: [sitemap()],
  vite: {
    plugins: [tailwindcss()],
  },
});
```

**No adapter.** Static output goes directly to `dist/`.

### Pattern 2: Layout with FOUC Prevention and Font Preloading

```astro
---
// src/layouts/Layout.astro
import "@fontsource-variable/geist";
import "@fontsource/inter/400.css";
import "@fontsource/inter/500.css";
import "@fontsource/inter/600.css";
import "@fontsource/inter/700.css";
import "../styles/global.css";

interface Props {
  title: string;
  description?: string;
}

const { title, description = "Russell Moore — Engineering Leader & Builder" } = Astro.props;
---

<!doctype html>
<html lang="en" class="dark">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content={description} />
    <title>{title}</title>
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
    <!-- FOUC prevention: inline background before any external CSS loads -->
    <style>
      html { background-color: #0a0a0f; color: #f5f5f5; }
    </style>
  </head>
  <body class="min-h-screen bg-background font-sans text-foreground antialiased">
    <slot />
  </body>
</html>
```

**Key points:**
- `class="dark"` hardcoded on `<html>` (D-07)
- Inline `<style>` for immediate dark background (D-08)
- Font imports in frontmatter so Astro bundles them at build time
- Font preloading handled by Astro's built-in asset pipeline when fonts are imported in frontmatter

### Pattern 3: Tailwind v4 CSS-Based Config

```css
/* src/styles/global.css */
@import "tailwindcss";

/* Enable class-based dark mode (required for dark: variant to work) */
@custom-variant dark (&:where(.dark, .dark *));

/* Design tokens */
@theme {
  --color-background: #0a0a0f;
  --color-foreground: #f5f5f5;
  --color-muted: #a1a1aa;
  --color-accent: #7c3aed;
  --color-accent-hover: #8b5cf6;
  --font-sans: "Geist Variable", "Inter", system-ui, sans-serif;
}
```

**Critical:** The `@custom-variant dark` line is REQUIRED for `dark:` utility classes to work with `class="dark"`. Without it, all `dark:` prefixed classes silently fail. This is the #1 pitfall for Tailwind v4 dark mode.

### Pattern 4: Content Collections with Glob Loader

```typescript
// src/content.config.ts
import { defineCollection, z } from "astro:content";
import { glob } from "astro/loaders";

const projects = defineCollection({
  loader: glob({ pattern: "**/*.md", base: "./src/content/projects" }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    stack: z.array(z.string()),
    status: z.enum(["Live", "In Development", "Archived"]),
    url: z.string().url().optional(),
    featured: z.boolean().default(false),
    order: z.number().default(0),
  }),
});

const writing = defineCollection({
  loader: glob({ pattern: "**/*.md", base: "./src/content/writing" }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    pubDate: z.coerce.date(),
    tags: z.array(z.string()).default([]),
    draft: z.boolean().default(true),
  }),
});

export const collections = { projects, writing };
```

**Schema field mapping from decisions:**
- D-13: `status` enum with 3 values (Live, In Development, Archived)
- D-14: `featured` boolean
- D-15: `stack` string array
- D-16: `url` optional string
- D-17: `tags` free-form string array
- D-18: `draft` boolean (default true)

### Pattern 5: Wrangler Static Asset Config

```toml
# wrangler.toml
name = "russellkmoore-me"
compatibility_date = "2026-03-26"

[assets]
directory = "./dist"
```

This is the minimal config for static asset serving. No `main` field needed -- wrangler serves files from `dist/` directly on Cloudflare's edge network.

### Pattern 6: npm Scripts

```json
{
  "scripts": {
    "dev": "astro dev",
    "build": "astro build",
    "preview": "astro preview",
    "deploy": "astro build && wrangler deploy",
    "check": "astro check"
  }
}
```

### Anti-Patterns to Avoid
- **Using `@astrojs/tailwind` integration:** Deprecated for Tailwind v4. Use `@tailwindcss/vite` directly.
- **Creating `tailwind.config.js`:** Tailwind v4 uses CSS-first config. JS config is legacy.
- **Creating `postcss.config.js`:** Tailwind v4 includes PostCSS internally.
- **Placing content config at `src/content/config.ts`:** Old location. Astro 5+ uses `src/content.config.ts`.
- **Using `type: 'content'` without `loader`:** Astro 5+ requires explicit glob loader.
- **Installing `@astrojs/cloudflare` for static output:** Adapter is for SSR only.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Font loading/optimization | Custom @font-face declarations | @fontsource-variable/geist package | Handles subsetting, format selection, CSS generation |
| Content schema validation | Custom frontmatter parsing | Astro content collections + Zod | Build-time validation, TypeScript types, glob loader |
| CSS utility framework | Custom CSS design system | Tailwind CSS v4 | Consistent tokens, responsive utilities, purged output |
| Static site generation | Custom build scripts | Astro build pipeline | Handles routing, asset optimization, HTML generation |
| Cloudflare deployment | Custom upload scripts | Wrangler CLI | Handles asset upload, routing, cache invalidation |

## Common Pitfalls

### Pitfall 1: Tailwind v4 Dark Mode Silent Failure
**What goes wrong:** `dark:bg-*` and all `dark:` prefixed utilities produce no CSS output
**Why it happens:** Tailwind v4 removed the JS `darkMode: 'class'` config. Without `@custom-variant dark (&:where(.dark, .dark *))` in CSS, the dark variant is undefined.
**How to avoid:** Add `@custom-variant dark` line in global.css BEFORE any utility usage
**Warning signs:** Elements show no dark mode styles despite `class="dark"` on html

### Pitfall 2: Content Collections Config Wrong Location
**What goes wrong:** `getCollection()` returns empty arrays, or build errors about missing collections
**Why it happens:** Tutorials show old `src/content/config.ts` path. Astro 5+ expects `src/content.config.ts`
**How to avoid:** Place at `src/content.config.ts`. Use glob loader. Every collection needs a `loader` property.
**Warning signs:** TypeScript errors about missing `loader`, empty collection results

### Pitfall 3: FOUC (White Flash) on Dark Theme
**What goes wrong:** Brief white flash before dark background appears
**Why it happens:** External CSS loads after initial paint; browser default background is white
**How to avoid:** Inline `<style>html { background-color: #0a0a0f; }</style>` in `<head>` (D-08)
**Warning signs:** Visible on hard refresh, especially on throttled connections

### Pitfall 4: Font Layout Shift (CLS)
**What goes wrong:** Text visibly reflows when Geist Sans loads, replacing fallback font
**Why it happens:** Geist Sans and system/Inter fonts have different metrics
**How to avoid:** Import fonts in Layout.astro frontmatter (Astro bundles at build time). The variable font is a single file covering all weights, which loads faster than multiple static weight files.
**Warning signs:** Lighthouse CLS > 0.1, visible text size change on load

### Pitfall 5: Astro 6 Version Drift
**What goes wrong:** `npm create astro@latest` installs Astro 6.x which breaks adapter compatibility
**Why it happens:** Astro 6.1.1 is now the `latest` tag on npm
**How to avoid:** After scaffolding, pin to `astro@^5.18.1` in package.json. Run `npm install astro@^5.18.1` to downgrade if needed.
**Warning signs:** Version in package.json starts with 6.x

### Pitfall 6: Wrangler Deploy Without Build
**What goes wrong:** Deploying stale or missing `dist/` contents
**Why it happens:** Running `wrangler deploy` without `astro build` first
**How to avoid:** Use combined npm script: `"deploy": "astro build && wrangler deploy"`
**Warning signs:** Live site shows old content or 404s

## Code Examples

### Seed Content: Mercora Project
```markdown
---
title: "Mercora"
description: "AI-native e-commerce platform combining composable commerce with intelligent product discovery, recommendations, and automated merchandising."
stack: ["Next.js", "Python", "Anthropic API", "Medusa.js"]
status: "In Development"
featured: true
order: 1
---

Mercora reimagines e-commerce with AI at its core...
```

### Seed Content: RecompAI Project
```markdown
---
title: "RecompAI"
description: "AI-powered coaching platform that provides personalized development recommendations for engineering leaders and their teams."
stack: ["TypeScript", "AI/ML", "Cloud Infrastructure"]
status: "In Development"
featured: true
order: 2
---

RecompAI delivers personalized coaching...
```

### Seed Content: Writing Entry (Draft)
```markdown
---
title: "The Case for Executives Who Still Ship Code"
description: "Why hands-on technical leadership produces better outcomes than pure management."
pubDate: 2026-03-26
tags: ["Leadership", "Engineering Culture"]
draft: true
---

Content placeholder...
```

### Minimal Index Page (Phase 1 proof-of-concept)
```astro
---
// src/pages/index.astro
import Layout from "../layouts/Layout.astro";
import { getCollection } from "astro:content";

const projects = await getCollection("projects");
const publishedWriting = await getCollection("writing", ({ data }) => !data.draft);
---

<Layout title="Russell Moore — Engineering Leader & Builder">
  <main class="mx-auto max-w-4xl px-6 py-24">
    <h1 class="text-4xl font-bold text-foreground">Russell Moore</h1>
    <p class="mt-4 text-lg text-muted">Engineering Leader & Builder</p>

    <section class="mt-16">
      <h2 class="text-2xl font-semibold text-foreground">Projects ({projects.length})</h2>
      <ul class="mt-4 space-y-4">
        {projects.map((project) => (
          <li class="rounded-lg border border-white/10 bg-white/5 p-4">
            <h3 class="font-semibold text-foreground">{project.data.title}</h3>
            <p class="text-sm text-muted">{project.data.description}</p>
            <div class="mt-2 flex gap-2">
              {project.data.stack.map((tech) => (
                <span class="rounded bg-accent/20 px-2 py-0.5 text-xs text-accent">{tech}</span>
              ))}
            </div>
          </li>
        ))}
      </ul>
    </section>

    <section class="mt-16">
      <h2 class="text-2xl font-semibold text-foreground">Writing</h2>
      {publishedWriting.length > 0 ? (
        <p>Published posts here</p>
      ) : (
        <p class="mt-4 text-muted">Coming soon.</p>
      )}
    </section>
  </main>
</Layout>
```

### Prettier Configuration
```json
{
  "semi": true,
  "singleQuote": false,
  "tabWidth": 2,
  "trailingComma": "all",
  "plugins": ["prettier-plugin-astro", "prettier-plugin-tailwindcss"]
}
```

Note: `prettier-plugin-tailwindcss` MUST be last in the plugins array.

### TypeScript Configuration
```json
{
  "extends": "astro/tsconfigs/strict"
}
```

Astro provides built-in tsconfig presets. The `strict` preset enables strict mode with Astro-specific settings.

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| `@astrojs/tailwind` integration | `@tailwindcss/vite` plugin | Astro 5.2 (late 2024) | Direct Vite plugin, no wrapper needed |
| `tailwind.config.js` | `@theme` in CSS | Tailwind v4 (Jan 2025) | CSS-first config, no JS file |
| `src/content/config.ts` | `src/content.config.ts` | Astro v5 (2024) | New file location at src/ root |
| `type: 'content'` collections | `glob()` loader | Astro v5 Content Layer | Explicit loaders, no implicit folder convention |
| `slug` field in collections | `id` field | Astro v5 | `slug` removed, use `id` instead |
| `darkMode: 'class'` in JS | `@custom-variant dark` in CSS | Tailwind v4 (Jan 2025) | CSS-first variant declaration |
| Cloudflare Pages | Cloudflare Workers | 2025 | Workers is the recommended deployment target |
| `wrangler pages deploy` | `wrangler deploy` | Wrangler v4 | Unified deploy command |

## Environment Availability

| Dependency | Required By | Available | Version | Fallback |
|------------|------------|-----------|---------|----------|
| Node.js | Everything | Yes | v22.14.0 | -- |
| npm | Package management | Yes | 11.11.0 | -- |
| wrangler | Cloudflare deploy (INFRA-01) | Via npx | 4.77.0 | Install as dev dependency |

**Missing dependencies with no fallback:** None

**Missing dependencies with fallback:**
- Wrangler is not globally installed but is available via `npx wrangler` and will be installed as a dev dependency

## Project Constraints (from CLAUDE.md)

- **Tech stack**: Astro + TypeScript + Tailwind CSS + @astrojs/cloudflare -- decided (note: adapter conflicts with static output, see Conflict section)
- **Deployment**: Cloudflare Workers via wrangler
- **Font**: Geist Sans via @fontsource, Inter fallback
- **Design**: Dark theme with violet accent, linear.app/vercel.com energy
- **Output mode**: Static (`output: "static"` in Astro config)
- **Avoid**: @astrojs/tailwind, tailwind.config.js, postcss.config.js, Astro 6, @astrojs/cloudflare v13, Squoosh, @fontsource/geist-sans (static), React/Vue/Svelte, Cloudflare Pages
- **GSD Workflow**: All file changes through GSD commands

## Open Questions

1. **Adapter vs. No Adapter (D-01 conflict)**
   - What we know: Official docs say adapter is not for static output. D-01 says use it from day one.
   - What's unclear: Whether user will accept research-informed deviation from D-01
   - Recommendation: Skip adapter in Phase 1; add it when switching output mode. Flag to user.

2. **Font preload path**
   - What we know: The font file is `geist-latin-wght-normal.woff2` inside the @fontsource-variable/geist package
   - What's unclear: Exact path after Astro bundles it (Astro hashes asset filenames)
   - Recommendation: Import in Layout frontmatter and let Astro's build pipeline handle optimization. Astro automatically adds preload hints for imported fonts. Manual `<link rel="preload">` may not be needed -- verify after first build.

3. **Custom domain wrangler config**
   - What we know: `wrangler.toml` handles static assets with `[assets]` config
   - What's unclear: Whether custom domain routing needs additional wrangler config or is handled via Cloudflare dashboard
   - Recommendation: Custom domain is configured in Cloudflare dashboard (DNS + Workers route), not in wrangler.toml. The wrangler config only needs `name`, `compatibility_date`, and `assets.directory`.

## Sources

### Primary (HIGH confidence)
- npm registry -- all package versions verified 2026-03-26
- [Astro Cloudflare Deployment Docs](https://docs.astro.build/en/guides/deploy/cloudflare/) -- static deployment without adapter
- [Astro Cloudflare Adapter Docs](https://docs.astro.build/en/guides/integrations-guide/cloudflare/) -- adapter is for on-demand rendering only
- [Cloudflare Workers Astro Framework Guide](https://developers.cloudflare.com/workers/framework-guides/web-apps/astro/) -- wrangler config examples
- `.planning/research/STACK.md` -- prior verified stack research
- `.planning/research/PITFALLS.md` -- prior verified pitfalls research
- `.planning/research/ARCHITECTURE.md` -- prior verified architecture research

### Secondary (MEDIUM confidence)
- [Tailwind v4 Dark Mode Discussion #16517](https://github.com/tailwindlabs/tailwindcss/discussions/16517) -- @custom-variant syntax
- [Astro v6 Static + Cloudflare Bug #15650](https://github.com/withastro/astro/issues/15650) -- adapter+static conflict evidence

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH -- all versions verified against npm registry
- Architecture: HIGH -- patterns from official docs and prior research
- Pitfalls: HIGH -- verified via official docs, GitHub issues, and prior research
- Adapter conflict: HIGH -- official docs explicitly state adapter is for on-demand rendering

**Research date:** 2026-03-26
**Valid until:** 2026-04-26 (stable ecosystem, 30-day window)
