# Phase 7: MDX Component Library - Research

**Researched:** 2026-03-27
**Domain:** Astro MDX components, CSS scroll-snap, HTML dialog lightbox, build-time metadata fetching
**Confidence:** HIGH

## Summary

This phase builds 6 reusable Astro components for MDX content: ScreenshotCarousel, InteractiveScreenshot, Callout, CodeComparison, Figure, and LinkCard. All are vanilla JS + Astro components with no framework dependencies.

The project already has strong patterns to follow: the TimelineBar tooltip JS pattern for InteractiveScreenshot hotspots, the Card component for styling consistency, and Shiki already configured with `github-dark-default` theme. The MDX integration is in place with `@astrojs/mdx@4.3.14` and content renders via `render()` from `astro:content` in both `[...slug].astro` pages.

**Primary recommendation:** Use the layout-level `components` prop on `<Content />` to auto-register all 6 components across both project and writing pages -- no per-file imports needed. Place all components in `src/components/mdx/` with a barrel export.

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- **D-01:** ScreenshotCarousel: Horizontal scroll with CSS scroll-snap + optional arrow navigation
- **D-02:** Click image to open in lightbox (full-screen overlay with close button)
- **D-03:** Captions below each image (from alt text or explicit caption prop)
- **D-04:** Works for both projects and writing
- **D-05:** InteractiveScreenshot: Base image with positioned hotspot indicators (pulsing dots with numbers)
- **D-06:** Hover/focus on hotspot shows tooltip card (same pattern as timeline milestones -- JS fixed-position)
- **D-07:** Mobile: tap to toggle tooltip (no hover on touch devices)
- **D-08:** Hotspots defined via props array: `[{ x: number, y: number, label: string, detail: string }]`
- **D-09:** Callout: 4 variants: info (blue), warning (amber), tip (green), note (muted/gray)
- **D-10:** Left border accent + icon + content area. Dark theme appropriate colors
- **D-11:** Usage: `<Callout type="tip">Content here</Callout>`
- **D-12:** CodeComparison: Side-by-side code blocks with "Before" / "After" labels (or custom labels)
- **D-13:** Stacks vertically on mobile
- **D-14:** Uses existing Shiki syntax highlighting (github-dark-default theme)
- **D-15:** Figure: Image with optional caption, optional border/shadow, optional width control
- **D-16:** Caption renders as `<figcaption>` for semantic HTML
- **D-17:** Usage: `<Figure src="/path/to/image.png" caption="Dashboard overview" />`
- **D-18:** LinkCard: Rich preview card for external links -- shows title, description, and domain
- **D-19:** Fetches metadata at build time (not runtime) for static output compatibility
- **D-20:** Fallback: if metadata unavailable, render as styled link with URL domain
- **D-21:** All vanilla JS + Astro components. No React/Vue/Svelte
- **D-22:** All respect dark theme tokens from global.css
- **D-23:** All components go in `src/components/mdx/` directory
- **D-24:** Export barrel file for easy importing in MDX

### Claude's Discretion
- Exact colors for callout variants
- Lightbox animation style
- Hotspot pulse animation CSS
- CodeComparison layout breakpoint
- LinkCard metadata extraction approach
- Whether to auto-register components globally or require explicit imports per MDX file

### Deferred Ideas (OUT OF SCOPE)
- Video embed component
- Table of contents component
- Mermaid diagram rendering in MDX
</user_constraints>

## Architecture Patterns

### Recommended Project Structure
```
src/components/mdx/
  Callout.astro          # Simplest -- pure HTML/CSS, slot for children
  Figure.astro           # Pure HTML/CSS, no JS needed
  CodeComparison.astro   # Uses Astro Code component for Shiki rendering
  ScreenshotCarousel.astro  # CSS scroll-snap + vanilla JS for arrows/lightbox
  InteractiveScreenshot.astro  # Positioned hotspots + vanilla JS tooltips
  LinkCard.astro         # Build-time fetch in frontmatter script
  index.ts               # Barrel export for easy importing
```

### Pattern 1: Component Auto-Registration via Content Pages

**What:** Register all MDX components at the page level by passing them to `<Content />` via the `components` prop, rather than importing them in every MDX file.

**When to use:** When you have a fixed set of components used across all MDX content.

**How it works in this project:**

Currently both `src/pages/projects/[...slug].astro` and `src/pages/writing/[...slug].astro` render MDX like this:
```astro
const { Content } = await render(project);
---
<Content />
```

Change to:
```astro
import * as mdxComponents from "../../components/mdx";
const { Content } = await render(project);
---
<Content components={mdxComponents} />
```

This makes all exported components from the barrel file available in every MDX file without per-file imports. MDX authors just use `<Callout>`, `<Figure>`, etc. directly.

**Tradeoff vs explicit imports:** Auto-registration is cleaner for content authors but means all component JS/CSS ships even if unused on a page. For 6 small components with minimal JS, this is negligible. For this project, auto-registration is the right call -- it keeps MDX files clean and content-focused.

**Tradeoff vs astro-auto-import package:** The `astro-auto-import` npm package uses a remark plugin to inject imports. It adds a dependency and complexity. The `components` prop approach is built-in, requires zero dependencies, and is documented in official Astro MDX docs. Use the built-in approach.

### Pattern 2: Vanilla JS in Astro Components for MDX

**What:** Interactive components use `<script>` tags with vanilla JS that query the DOM after render.

**Existing pattern in project:** `TimelineBar.astro` uses this exact pattern -- a `<script>` block at the bottom that queries elements and attaches event listeners.

**Key rules for MDX context:**
- Scripts in Astro components are bundled and deduplicated by default
- Use `document.querySelectorAll` with specific selectors (data attributes or unique classes) to avoid conflicts when multiple instances exist on one page
- For components that may appear multiple times (Callout, Figure), keep JS minimal or zero
- For singleton-like components (ScreenshotCarousel lightbox), a single script block is fine

### Pattern 3: Slots for Children in MDX

**What:** Astro components use `<slot />` to receive children. In MDX, content between opening and closing tags becomes the default slot.

**Example -- Callout in MDX:**
```mdx
<Callout type="tip">
  This is a tip. You can include **markdown** and `code` here.
</Callout>
```

The Callout component receives this content via `<slot />`:
```astro
---
interface Props {
  type?: "info" | "warning" | "tip" | "note";
}
const { type = "info" } = Astro.props;
---
<div class={`callout callout-${type}`}>
  <slot />
</div>
```

**Limitation:** Named slots work in `.astro` files but have issues in MDX (Astro issue #14311). Stick to the default slot only. For components needing multiple content areas (like CodeComparison), use props instead of named slots.

### Anti-Patterns to Avoid
- **Named slots in MDX:** `Astro.slots.render` with named slots has known bugs in MDX context. Use props for structured data, default slot for content.
- **React/Vue for interactivity:** Project constraint forbids it. Vanilla JS in `<script>` tags handles all interactivity.
- **Client-side metadata fetching for LinkCard:** Static output means no server runtime. Fetch at build time in the component's frontmatter script.
- **Heavy lightbox libraries:** GLightbox, PhotoSwipe, etc. are overkill. The HTML `<dialog>` element with `showModal()` provides focus trapping, backdrop, and Escape-to-close natively.

## Component Implementation Details

### ScreenshotCarousel -- CSS Scroll-Snap + Dialog Lightbox

**Scroll container pattern:**
```css
.carousel {
  display: flex;
  gap: 1rem;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  scroll-behavior: smooth;
  -webkit-overflow-scrolling: touch;
}
.carousel > * {
  scroll-snap-align: start;
  flex-shrink: 0;
}
```

**Arrow navigation JS:**
```javascript
function scrollByOne(container, direction) {
  const child = container.children[0];
  if (!child) return;
  const scrollAmount = child.offsetWidth + 16; // width + gap
  container.scrollBy({ left: direction * scrollAmount, behavior: "smooth" });
}
```

**Lightbox via HTML `<dialog>`:**
```html
<dialog id="lightbox-dialog" class="lightbox-dialog">
  <img src="" alt="" />
  <button class="lightbox-close" aria-label="Close">&times;</button>
</dialog>
```

Key dialog features used:
- `dialog.showModal()` -- creates modal with focus trap and backdrop
- `dialog::backdrop` -- CSS pseudo-element for dark overlay styling
- Escape key closes automatically (native behavior)
- Click backdrop to close via `dialog.addEventListener("click", (e) => { if (e.target === dialog) dialog.close(); })`

**Confidence:** HIGH -- CSS scroll-snap and `<dialog>` are well-supported in all modern browsers. The pattern is well-documented.

### InteractiveScreenshot -- Positioned Hotspots + Tooltips

**Reuse existing pattern:** The `TimelineBar.astro` tooltip pattern (lines 90-142) is directly applicable. Key elements:
- Fixed-position tooltip div outside the scroll context
- `getBoundingClientRect()` for positioning
- Viewport clamping with `Math.max/Math.min`
- `mouseenter`/`mouseleave` for desktop, focus/blur for keyboard

**Mobile touch handling (D-07):**
```javascript
// Detect touch device
const isTouch = "ontouchstart" in window;

if (isTouch) {
  node.addEventListener("click", (e) => {
    e.preventDefault();
    if (activeNode === node) {
      hideTooltip();
      activeNode = null;
    } else {
      showTooltip(e);
      activeNode = node;
    }
  });
} else {
  node.addEventListener("mouseenter", showTooltip);
  node.addEventListener("mouseleave", hideTooltip);
}
```

**Hotspot pulse animation:**
```css
@keyframes pulse-ring {
  0% { transform: scale(1); opacity: 0.8; }
  100% { transform: scale(2.5); opacity: 0; }
}
.hotspot::before {
  content: "";
  position: absolute;
  inset: -4px;
  border-radius: 50%;
  background: rgba(167, 139, 250, 0.4);
  animation: pulse-ring 2s ease-out infinite;
}
```

**Confidence:** HIGH -- direct reuse of existing project pattern.

### Callout -- Pure CSS Component

**Zero JS required.** This is the simplest component.

**Recommended variant colors (dark theme appropriate):**

| Variant | Border Color | Background | Icon |
|---------|-------------|------------|------|
| info | `border-blue-500` | `bg-blue-500/10` | `lucide:info` |
| warning | `border-amber-500` | `bg-amber-500/10` | `lucide:alert-triangle` |
| tip | `border-emerald-500` | `bg-emerald-500/10` | `lucide:lightbulb` |
| note | `border-zinc-500` | `bg-zinc-500/10` | `lucide:pencil` |

All with `border-l-4` for the left accent, matching the linear.app aesthetic (subtle, not loud).

**Confidence:** HIGH -- pure HTML/CSS, uses existing astro-icon integration.

### CodeComparison -- Astro Code Component

**Key finding:** Astro provides a built-in `<Code />` component that renders Shiki-highlighted code programmatically. It accepts `code`, `lang`, and `theme` props. This is distinct from markdown code fences -- it works in `.astro` component templates.

```astro
---
import { Code } from "astro:components";
interface Props {
  before: string;
  after: string;
  lang?: string;
  beforeLabel?: string;
  afterLabel?: string;
}
const { before, after, lang = "typescript", beforeLabel = "Before", afterLabel = "After" } = Astro.props;
---
<div class="grid grid-cols-1 md:grid-cols-2 gap-4">
  <div>
    <span class="text-xs text-muted">{beforeLabel}</span>
    <Code code={before} lang={lang} theme="github-dark-default" />
  </div>
  <div>
    <span class="text-xs text-muted">{afterLabel}</span>
    <Code code={after} lang={lang} theme="github-dark-default" />
  </div>
</div>
```

**Important:** The `<Code />` component does NOT inherit `shikiConfig` from `astro.config.mjs`. The theme must be passed explicitly. Use `"github-dark-default"` to match the project's configured theme.

**MDX usage:**
```mdx
<CodeComparison
  lang="typescript"
  before={`const x = 1;\nconst y = 2;`}
  after={`const { x, y } = config;`}
/>
```

**Breakpoint recommendation:** `md` (768px) -- stacks vertically below, side-by-side above. Matches the project's existing responsive patterns.

**Confidence:** HIGH -- Astro `Code` component is well-documented and built-in.

### Figure -- Pure HTML Component

**Zero JS required.** Semantic HTML with optional styling.

```astro
---
interface Props {
  src: string;
  alt?: string;
  caption?: string;
  width?: string;
  bordered?: boolean;
  shadow?: boolean;
}
---
<figure>
  <img src={src} alt={alt ?? caption ?? ""} class:list={[...]} loading="lazy" />
  {caption && <figcaption>{caption}</figcaption>}
</figure>
```

**Confidence:** HIGH -- trivial component.

### LinkCard -- Build-Time Metadata Fetching

**This is the most complex component.** Must fetch og:title, og:description from external URLs during `npm run build`.

**Approach:** Use `fetch()` in the component's frontmatter script. In Astro static mode, component frontmatter runs at build time, not runtime. This means `fetch()` in the `---` block executes during `npm run build`.

```astro
---
interface Props {
  href: string;
}
const { href } = Astro.props;

let title = "";
let description = "";
let domain = "";

try {
  domain = new URL(href).hostname;
  const response = await fetch(href, {
    headers: { "User-Agent": "Mozilla/5.0 (compatible; AstroBot/1.0)" },
    signal: AbortSignal.timeout(5000),
  });
  const html = await response.text();

  // Extract og:title or <title>
  const ogTitle = html.match(/<meta[^>]*property="og:title"[^>]*content="([^"]*)"/) ??
                  html.match(/<meta[^>]*content="([^"]*)"[^>]*property="og:title"/);
  const titleTag = html.match(/<title[^>]*>([^<]*)<\/title>/);
  title = ogTitle?.[1] ?? titleTag?.[1] ?? domain;

  // Extract og:description or meta description
  const ogDesc = html.match(/<meta[^>]*property="og:description"[^>]*content="([^"]*)"/) ??
                 html.match(/<meta[^>]*content="([^"]*)"[^>]*property="og:description"/);
  const metaDesc = html.match(/<meta[^>]*name="description"[^>]*content="([^"]*)"/) ??
                   html.match(/<meta[^>]*content="([^"]*)"[^>]*name="description"/);
  description = ogDesc?.[1] ?? metaDesc?.[1] ?? "";
} catch {
  // Fallback: render as styled link with domain
  domain = domain || href;
}
---
```

**Pitfalls to handle:**
- Timeout: Some URLs are slow. Use `AbortSignal.timeout(5000)` to cap at 5 seconds.
- Redirects: `fetch()` follows redirects by default.
- User-Agent: Some sites block requests without a User-Agent header.
- Build failure: A failed fetch must NOT crash the build. Wrap in try/catch with graceful fallback.
- Rate limiting: If many LinkCards fetch from the same domain, builds could get rate-limited. Not a concern for a personal portfolio with a handful of links.

**Confidence:** MEDIUM -- The approach is sound (fetch in frontmatter = build-time), but edge cases (sites that block bots, slow responses) could cause issues. The fallback (D-20) handles these gracefully.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Syntax highlighting | Custom code tokenizer | Astro `<Code />` component | Shiki is already configured, built-in component handles it |
| Modal/lightbox | Custom overlay + focus trap | HTML `<dialog>` element | Native focus trapping, backdrop, Escape key, accessibility built-in |
| Icon rendering | Inline SVGs | `astro-icon` + `lucide` icons | Already installed and used throughout the project |
| Carousel scrolling | Custom scroll physics | CSS `scroll-snap-type` | Hardware-accelerated, native momentum on mobile |

## Common Pitfalls

### Pitfall 1: Multiple Component Instances on Same Page
**What goes wrong:** JS scripts target elements by ID, second instance breaks.
**Why it happens:** Astro deduplicates `<script>` tags -- only one copy runs per page.
**How to avoid:** Use `document.querySelectorAll` with data attributes, not `getElementById`. Loop over all instances. For the lightbox dialog, use a single shared dialog element (singleton pattern).
**Warning signs:** Second carousel on a page doesn't work, or clicking any image opens the same lightbox image.

### Pitfall 2: Shiki Theme Mismatch in CodeComparison
**What goes wrong:** Code blocks in CodeComparison look different from markdown code fences.
**Why it happens:** `<Code />` component does not inherit `shikiConfig` from astro.config.mjs.
**How to avoid:** Explicitly pass `theme="github-dark-default"` to every `<Code />` instance.
**Warning signs:** Different code block backgrounds or color schemes.

### Pitfall 3: LinkCard Crashing the Build
**What goes wrong:** A single failed fetch causes `npm run build` to fail.
**Why it happens:** Unhandled fetch errors or timeouts.
**How to avoid:** Wrap all fetch logic in try/catch. Always render something -- even if it's just a styled link to the URL.
**Warning signs:** Build fails with network errors or timeout exceptions.

### Pitfall 4: Named Slots Not Working in MDX
**What goes wrong:** Content passed to named slots renders as default slot or disappears.
**Why it happens:** Known Astro issue (#14311) -- `Astro.slots.render` with MDX has bugs.
**How to avoid:** Only use the default `<slot />`. Pass structured data through props instead of named slots.
**Warning signs:** Component renders empty sections or all content in wrong position.

### Pitfall 5: Tailwind Classes Not Generated for Dynamic Values
**What goes wrong:** Dynamic class strings like `` `bg-${color}-500` `` produce no CSS.
**Why it happens:** Tailwind scans source files at build time. Dynamically constructed class names are not detected.
**How to avoid:** Use complete class strings in a lookup object: `const colors = { info: "bg-blue-500/10 border-blue-500", ... }`.
**Warning signs:** Callout variants have no colors.

### Pitfall 6: Carousel Scroll Snap on Resize
**What goes wrong:** After window resize, scroll position doesn't align to snap points.
**Why it happens:** Item widths change but scroll position stays at old pixel value.
**How to avoid:** CSS handles this natively if using `scroll-snap-type: x mandatory`. No JS resize handler needed.
**Warning signs:** Not actually a problem with pure CSS approach -- only affects JS-driven carousels.

## Code Examples

### Barrel Export Pattern (src/components/mdx/index.ts)
```typescript
export { default as Callout } from "./Callout.astro";
export { default as Figure } from "./Figure.astro";
export { default as CodeComparison } from "./CodeComparison.astro";
export { default as ScreenshotCarousel } from "./ScreenshotCarousel.astro";
export { default as InteractiveScreenshot } from "./InteractiveScreenshot.astro";
export { default as LinkCard } from "./LinkCard.astro";
```

### Auto-Registration in Page Templates
```astro
// In src/pages/projects/[...slug].astro and src/pages/writing/[...slug].astro
import * as mdxComponents from "../../components/mdx";
const { Content } = await render(project);
---
<ProjectLayout frontmatter={project.data}>
  <Content components={mdxComponents} />
</ProjectLayout>
```

### MDX Usage Example (in any .mdx file)
```mdx
## Architecture

<Callout type="tip">
  The system uses event sourcing for all state mutations.
</Callout>

<Figure src="/images/architecture.png" caption="System architecture overview" bordered />

<CodeComparison
  lang="typescript"
  beforeLabel="Legacy"
  afterLabel="Refactored"
  before={`function getData() {\n  return fetch('/api').then(r => r.json());\n}`}
  after={`async function getData() {\n  const res = await fetch('/api');\n  return res.json();\n}`}
/>

<LinkCard href="https://docs.astro.build" />
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Custom overlay + focus trap JS | HTML `<dialog>` with `showModal()` | 2022+ (widely supported) | No need for focus trap libraries, backdrop is native |
| JS-based carousels (Slick, Swiper) | CSS `scroll-snap-type` | 2020+ (widely supported) | Zero JS for basic carousel behavior |
| Per-MDX-file component imports | `components` prop on `<Content />` | Astro MDX integration | Cleaner MDX files, centralized registration |

## Open Questions

1. **Astro Code component in MDX components**
   - What we know: `<Code />` from `astro:components` works in `.astro` files and accepts code/lang/theme props.
   - What's unclear: Whether passing multi-line code strings through MDX props has escaping issues.
   - Recommendation: Test with template literals containing backticks. May need to use `{String.raw\`...\`}` or pass as a prop with curly braces.

2. **LinkCard caching across builds**
   - What we know: Each `npm run build` re-fetches all URLs.
   - What's unclear: Whether repeated builds during development will be slow or get rate-limited.
   - Recommendation: Not a real concern for a portfolio site with few LinkCards. If it becomes an issue, cache responses in a temp file.

## Project Constraints (from CLAUDE.md)

- **Tech stack:** Astro + TypeScript + Tailwind CSS -- no alternatives
- **Output mode:** Static (`output: "static"`) -- no server-side runtime
- **No React/Vue/Svelte:** Zero client-side framework JS
- **Font:** Geist Sans via @fontsource-variable/geist
- **Design:** Dark theme with violet accent (#a78bfa), linear.app/vercel.com energy
- **Tailwind v4:** CSS-based config, no `tailwind.config.js`
- **Shiki theme:** `github-dark-default` (configured in astro.config.mjs)
- **Icons:** astro-icon + @iconify-json/lucide (already installed)
- **MDX:** @astrojs/mdx@4.3.14 (not v5, for Astro 5 compatibility)

## Sources

### Primary (HIGH confidence)
- Astro MDX integration docs -- components prop, slot behavior, MDX imports
- Astro syntax highlighting docs -- `<Code />` component API, Shiki configuration
- Project codebase -- `TimelineBar.astro` (tooltip pattern), `Card.astro` (styling), `global.css` (theme tokens), `astro.config.mjs` (MDX + Shiki config)
- MDN CSS scroll-snap docs -- `scroll-snap-type`, `scroll-snap-align` behavior
- MDN `<dialog>` element docs -- `showModal()`, `::backdrop`, focus trapping

### Secondary (MEDIUM confidence)
- Polypane blog -- Dialog lightbox implementation pattern
- Builder.io blog -- CSS scroll-snap carousel patterns
- npm astro-auto-import -- evaluated and rejected in favor of built-in `components` prop

### Tertiary (LOW confidence)
- LinkCard metadata extraction approach -- sound in theory, edge cases around bot-blocking sites need testing

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH -- all components use existing project libraries (Astro, Tailwind, astro-icon, Shiki)
- Architecture: HIGH -- component registration via `components` prop is documented Astro MDX feature
- Pitfalls: HIGH -- based on known Astro MDX issues and project-specific patterns
- LinkCard build-time fetch: MEDIUM -- approach is correct but edge cases exist

**Research date:** 2026-03-27
**Valid until:** 2026-04-27 (stable domain -- Astro 5, CSS standards)
