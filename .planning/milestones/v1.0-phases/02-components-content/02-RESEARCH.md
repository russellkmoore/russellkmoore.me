# Phase 2: Components & Content - Research

**Researched:** 2026-03-26
**Domain:** Astro components, Tailwind v4 responsive design, CSS animations, sticky navigation
**Confidence:** HIGH

## Summary

Phase 2 transforms the Phase 1 scaffold into a complete single-page portfolio with six content sections, sticky navigation, and responsive layout. The existing foundation (Astro 5, Tailwind v4 CSS-first config, content collections, dark theme tokens) is solid and directly reusable. The primary technical concerns are: (1) astro-icon integration for lucide icons in nav/footer/CTAs, (2) CSS mesh gradient animation for the hero that remains performant and subtle, (3) scroll-triggered sticky nav background transition using vanilla JS IntersectionObserver, and (4) mobile hamburger menu with slide-in panel using pure Astro + vanilla JS (no framework needed).

All components are .astro files with zero client-side framework overhead. The only JavaScript needed is small vanilla scripts for: nav scroll behavior (IntersectionObserver), mobile menu toggle, and optionally smooth scroll polishing. These use Astro's `<script>` tags which are bundled and optimized automatically.

**Primary recommendation:** Build reusable components (Card, Badge, SectionHeader) first, then compose sections bottom-up. Use astro-icon with @iconify-json/lucide for all icons. Use CSS `scroll-behavior: smooth` on html element. Use IntersectionObserver with a sentinel div for transparent-to-solid nav transition.

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- **D-01:** Tagline tone is impact-focused -- "Building products at the intersection of AI and commerce" style
- **D-02:** Subtle mesh gradient background behind hero -- slow-moving color blobs (violet + dark blue), barely noticeable
- **D-03:** CTA buttons are text links with arrow icons, not buttons -- "See My Work ->" and "Let's Talk ->"
- **D-04:** Hero height ~60-70vh
- **D-05:** Section order: Hero -> About -> Projects -> Career -> Writing -> Contact
- **D-06:** Sticky nav transitions from transparent over hero to solid dark background on scroll
- **D-07:** Nav contains section text links only: About, Projects, Career, Writing, Contact (right side). Name/logo on left. No resume button
- **D-08:** Smooth scroll behavior (CSS scroll-behavior: smooth) for all anchor links
- **D-09:** Projects use featured card + list layout -- featured projects get larger hero-style cards
- **D-10:** About section: narrative paragraph above, 4 stats cards row below
- **D-11:** Career highlights in timeline style -- vertical timeline with connected dots/line
- **D-12:** Contact section as centered CTA block
- **D-13:** Gradient fade dividers between sections
- **D-14:** Mobile-first approach using Tailwind's sm:/md:/lg: modifiers
- **D-15:** Mobile nav: hamburger triggers slide-in panel from right
- **D-16:** Grid behavior on mobile: Claude's discretion per section
- **D-17:** First-person, direct voice for About narrative

### Claude's Discretion
- Footer layout (minimal, satisfying LAYOUT-03 with copyright + LinkedIn + GitHub icons)
- Grid breakpoint behavior per section (mobile stacking strategy)
- Exact gradient animation CSS (mesh gradient approach)
- Section heading component implementation details
- Badge and Card component internal styling
- Active nav link highlighting approach

### Deferred Ideas (OUT OF SCOPE)
None -- discussion stayed within phase scope
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| LAYOUT-01 | Layout.astro wraps all pages with consistent head, nav, and footer | Extend existing Layout.astro with Nav and Footer components in slot |
| LAYOUT-02 | Sticky nav with name/logo left, section links right, hamburger mobile | IntersectionObserver sentinel pattern for transparent->solid transition |
| LAYOUT-03 | Minimal footer with copyright + LinkedIn + GitHub icons | astro-icon with lucide:linkedin and lucide:github |
| LAYOUT-04 | Fully responsive across mobile, tablet, desktop | Tailwind v4 sm:/md:/lg: modifiers, mobile-first approach |
| COMP-01 | Reusable Card.astro component | Astro Props interface, slot-based composition, bg-white/5 border-white/10 established pattern |
| COMP-02 | Reusable Badge.astro component | Variant prop for tech-stack vs status styling |
| COMP-03 | Reusable SectionHeader.astro component | Consistent h2 styling with optional subtitle |
| HERO-01 | Hero displays name and tagline | text-5xl/text-6xl for name, text-xl for tagline, 60-70vh container |
| HERO-02 | Two CTA text links with arrows | Text links with lucide:arrow-right icon, anchor hrefs to #projects and #contact |
| HERO-03 | Subtle animated gradient background | CSS @keyframes with background-position animation on radial gradients |
| ABOUT-01 | Short narrative paragraph | First-person direct voice, text-lg text-muted |
| ABOUT-02 | 4 stats cards | Grid layout 2x2 on mobile, 4-col on desktop |
| PROJ-01 | Card grid from content collection | getCollection("projects"), featured card + compact list layout |
| PROJ-02 | Project cards with title, description, tech tags, status badge | Card + Badge composition |
| PROJ-03 | Featured projects visually distinguished | Larger card variant with more detail |
| CAREER-01 | 3 highlight cards for AT&T, Spark::red, Pivotree | Timeline component with vertical line and dots |
| CAREER-02 | Each card shows company, role, metrics | Structured data in component props or hardcoded in section |
| WRITE-01 | Card grid for published writing | getCollection with draft filter |
| WRITE-02 | Graceful "coming soon" when no published posts | Conditional render, all seeds are draft:true |
| WRITE-03 | Draft entries never render | Already implemented in Phase 1 filter pattern |
| CONTACT-01 | Email + LinkedIn links | Text links with lucide icons and arrow pattern |
| CONTACT-02 | Brief "what I'm open to" copy | Centered text block |
</phase_requirements>

## Project Constraints (from CLAUDE.md)

- **Tech stack:** Astro 5 + TypeScript + Tailwind CSS v4 + @tailwindcss/vite -- no alternatives
- **No frameworks:** No React/Vue/Svelte -- pure .astro components with vanilla JS
- **No heavy animations:** CSS transitions only, no GSAP/parallax
- **Tailwind v4:** CSS-first config, no tailwind.config.js, no postcss.config.js
- **Fonts:** Geist Sans variable + Inter fallback (already loaded in Layout.astro)
- **Output:** Static (output: "static")
- **Avoid:** @astrojs/tailwind (deprecated), Squoosh, @fontsource/geist-sans (static)

## Standard Stack

### Core (already installed)
| Library | Version | Purpose | Status |
|---------|---------|---------|--------|
| astro | ^5.18.1 | Static site framework | Installed |
| tailwindcss | ^4.2.2 | Utility-first CSS | Installed |
| @tailwindcss/vite | ^4.2.2 | Tailwind Vite plugin | Installed |
| @fontsource-variable/geist | ^5.2.8 | Geist Sans font | Installed |
| @fontsource/inter | ^5.2.8 | Inter fallback | Installed |

### New for Phase 2
| Library | Version | Purpose | Why |
|---------|---------|---------|-----|
| astro-icon | ^1.1.5 | SVG icon component | Wraps Iconify, SSR-rendered SVGs, zero JS. Required for nav, footer, CTAs |
| @iconify-json/lucide | ^1.2.99 | Lucide icon set | Clean minimal icons matching linear.app aesthetic. GitHub, LinkedIn, arrow-right, menu, x |

**Installation:**
```bash
npm install astro-icon @iconify-json/lucide
```

Then add to `astro.config.mjs`:
```javascript
import icon from "astro-icon";

export default defineConfig({
  integrations: [sitemap(), icon()],
  // ... rest unchanged
});
```

## Architecture Patterns

### Component Structure
```
src/
  components/
    Nav.astro              # Sticky nav with scroll detection
    MobileMenu.astro       # Slide-in hamburger panel (or inline in Nav)
    Footer.astro           # Minimal footer
    Card.astro             # Reusable card (COMP-01)
    Badge.astro            # Tech tag / status badge (COMP-02)
    SectionHeader.astro    # Section title + optional subtitle (COMP-03)
    sections/
      Hero.astro           # Hero with gradient, name, tagline, CTAs
      About.astro          # Narrative + stats grid
      Projects.astro       # Featured cards + list
      Career.astro         # Timeline
      Writing.astro        # Card grid or coming soon
      Contact.astro        # Centered CTA block
  layouts/
    Layout.astro           # Updated with Nav + Footer wrapping slot
  pages/
    index.astro            # Composes all sections in order
  styles/
    global.css             # Existing tokens (may add new tokens)
```

### Pattern 1: Astro Component Props with TypeScript
**What:** Type-safe component interfaces using Astro's Props pattern
**When:** Every reusable component

```astro
---
interface Props {
  variant?: "default" | "featured";
  class?: string;
}

const { variant = "default", class: className } = Astro.props;
---

<div class:list={["base-classes", { "featured-classes": variant === "featured" }, className]}>
  <slot />
</div>
```

### Pattern 2: astro-icon Usage
**What:** SSR-rendered SVG icons with zero client JS
**When:** All icon usage (nav, footer, CTAs, external links)

```astro
---
import { Icon } from "astro-icon/components";
---

<Icon name="lucide:arrow-right" class="size-4" />
<Icon name="lucide:github" class="size-5" />
<Icon name="lucide:linkedin" class="size-5" />
<Icon name="lucide:menu" class="size-6" />
<Icon name="lucide:x" class="size-6" />
```

Key icons needed: `arrow-right`, `github`, `linkedin`, `menu`, `x` (close), `mail`

### Pattern 3: IntersectionObserver Sentinel for Sticky Nav
**What:** Transparent nav over hero, solid on scroll -- without scroll event listeners
**When:** Nav background transition (D-06)

```astro
<!-- Sentinel div at top of page, zero height -->
<div id="nav-sentinel" class="absolute top-0 h-0"></div>

<nav id="site-nav" class="fixed top-0 z-50 w-full transition-colors duration-300">
  <!-- nav content -->
</nav>

<script>
  const sentinel = document.getElementById("nav-sentinel");
  const nav = document.getElementById("site-nav");

  const observer = new IntersectionObserver(
    ([entry]) => {
      if (entry.isIntersecting) {
        nav?.classList.remove("bg-background/95", "backdrop-blur-sm", "border-b", "border-white/10");
      } else {
        nav?.classList.add("bg-background/95", "backdrop-blur-sm", "border-b", "border-white/10");
      }
    },
    { threshold: 0 }
  );

  if (sentinel) observer.observe(sentinel);
</script>
```

### Pattern 4: Mobile Hamburger Menu (vanilla JS)
**What:** Slide-in panel from right, toggled by hamburger icon
**When:** Mobile nav (D-15)

```astro
<button id="menu-toggle" class="md:hidden" aria-label="Toggle menu">
  <Icon name="lucide:menu" class="size-6" id="menu-icon" />
</button>

<div id="mobile-menu" class="fixed inset-0 z-40 translate-x-full transition-transform duration-300 md:hidden">
  <div class="absolute inset-0 bg-black/50" id="menu-backdrop"></div>
  <div class="absolute right-0 top-0 h-full w-64 bg-background p-6">
    <button id="menu-close" aria-label="Close menu">
      <Icon name="lucide:x" class="size-6" />
    </button>
    <!-- nav links stacked -->
  </div>
</div>

<script>
  const toggle = document.getElementById("menu-toggle");
  const menu = document.getElementById("mobile-menu");
  const close = document.getElementById("menu-close");
  const backdrop = document.getElementById("menu-backdrop");

  function openMenu() {
    menu?.classList.remove("translate-x-full");
    menu?.classList.add("translate-x-0");
    document.body.style.overflow = "hidden";
  }

  function closeMenu() {
    menu?.classList.remove("translate-x-0");
    menu?.classList.add("translate-x-full");
    document.body.style.overflow = "";
  }

  toggle?.addEventListener("click", openMenu);
  close?.addEventListener("click", closeMenu);
  backdrop?.addEventListener("click", closeMenu);

  // Close on nav link click
  menu?.querySelectorAll("a").forEach((link) => {
    link.addEventListener("click", closeMenu);
  });
</script>
```

### Pattern 5: CSS Mesh Gradient Animation (Hero Background)
**What:** Subtle, slow-moving gradient blobs behind hero text
**When:** Hero section (D-02, HERO-03)

```css
.hero-gradient {
  position: absolute;
  inset: 0;
  overflow: hidden;
  z-index: 0;
}

.hero-gradient::before {
  content: "";
  position: absolute;
  inset: -50%;
  background: radial-gradient(circle at 30% 40%, rgba(124, 58, 237, 0.15) 0%, transparent 50%),
              radial-gradient(circle at 70% 60%, rgba(59, 130, 246, 0.08) 0%, transparent 50%),
              radial-gradient(circle at 50% 50%, rgba(124, 58, 237, 0.05) 0%, transparent 70%);
  animation: meshMove 20s ease-in-out infinite alternate;
}

@keyframes meshMove {
  0% { transform: translate(0, 0) scale(1); }
  33% { transform: translate(2%, -3%) scale(1.02); }
  66% { transform: translate(-2%, 2%) scale(0.98); }
  100% { transform: translate(1%, -1%) scale(1.01); }
}
```

Key principles:
- Very low opacity (0.05-0.15) so text remains highly readable
- Slow animation (20s+) so it feels atmospheric, not distracting
- Use `will-change: transform` for GPU acceleration
- Violet (#7c3aed) as primary color, blue as secondary -- matches accent token
- The pseudo-element is larger than container (inset: -50%) so movement doesn't reveal edges

### Pattern 6: Gradient Fade Section Dividers (D-13)
**What:** Soft gradient fades between sections instead of hard lines

```astro
<div class="h-px w-full bg-gradient-to-r from-transparent via-white/10 to-transparent" />
```

Or for more atmosphere, a taller fade:

```astro
<div class="h-24 bg-gradient-to-b from-transparent via-accent/5 to-transparent" />
```

### Pattern 7: Smooth Scroll
**What:** CSS-only smooth scrolling for anchor links (D-08)

Add to `global.css`:
```css
html {
  scroll-behavior: smooth;
}
```

Combined with nav offset for sticky header:
```css
html {
  scroll-padding-top: 5rem; /* matches nav height */
}
```

### Anti-Patterns to Avoid
- **Scroll event listeners for nav:** Use IntersectionObserver instead -- scroll listeners fire on every pixel and cause jank
- **JS scroll libraries (smooth-scroll, locomotive):** CSS scroll-behavior: smooth is sufficient for anchor navigation
- **Fixed positioning without z-index strategy:** Use z-50 for nav, z-40 for mobile menu overlay consistently
- **Inline styles for responsive:** Always use Tailwind responsive modifiers (sm:/md:/lg:), never media queries in style blocks
- **Importing full Iconify set:** Only install @iconify-json/lucide. astro-icon tree-shakes to only used icons in static builds

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| SVG icons | Inline SVG strings or custom icon component | astro-icon + @iconify-json/lucide | Handles accessibility (aria), sizing, optimization (svgo), 1000+ icons available |
| Responsive grid | Custom CSS grid/flexbox from scratch | Tailwind grid/flex utilities | grid-cols-1 md:grid-cols-2 lg:grid-cols-4 is battle-tested |
| Font loading | Manual @font-face declarations | @fontsource (already set up) | Handles FOUT, variable font subsets, weight optimization |
| Scroll detection | addEventListener("scroll") with throttle | IntersectionObserver API | Native browser API, no jank, no throttle logic needed |

## Common Pitfalls

### Pitfall 1: Astro Script Deduplication
**What goes wrong:** Putting `<script>` inside a component used multiple times -- Astro deduplicates and only runs it once.
**Why it happens:** Astro bundles scripts and deduplicates by default.
**How to avoid:** For nav/menu scripts, this is actually desired (only one nav). For per-instance behavior, use `<script is:inline>` or data attributes with a single delegated script.
**Warning signs:** Event listeners not attaching to all instances.

### Pitfall 2: Missing scroll-padding-top with Sticky Nav
**What goes wrong:** Anchor links scroll sections behind the sticky nav header.
**Why it happens:** Browser scrolls to element top, but nav covers it.
**How to avoid:** Add `scroll-padding-top: 5rem` (or nav height) to `html` in global.css.
**Warning signs:** Section headings hidden behind nav after clicking nav links.

### Pitfall 3: Mobile Menu Body Scroll Leak
**What goes wrong:** Background page scrolls while mobile menu is open.
**Why it happens:** Body overflow not locked when menu opens.
**How to avoid:** Set `document.body.style.overflow = "hidden"` on open, restore on close.
**Warning signs:** Page position shifts when closing menu.

### Pitfall 4: Gradient Animation Performance
**What goes wrong:** Gradient animation causes high CPU/GPU usage on mobile.
**Why it happens:** Animating background properties triggers repaints. Large gradient areas are expensive.
**How to avoid:** Animate `transform` only (translate/scale), not background-position. Use `will-change: transform`. Keep gradient on a pseudo-element so the layer is composited separately.
**Warning signs:** Battery drain, frame drops on mobile Safari.

### Pitfall 5: astro-icon Integration Registration
**What goes wrong:** `<Icon>` component renders nothing or throws "icon not found."
**Why it happens:** astro-icon must be added as an Astro integration in config, not just installed.
**How to avoid:** Add `icon()` to integrations array in astro.config.mjs AND install the @iconify-json/lucide package.
**Warning signs:** Build warnings about missing icons.

### Pitfall 6: Tailwind v4 Class Name Differences
**What goes wrong:** Using v3 class names that changed in v4.
**Why it happens:** Tailwind v4 renamed some utilities.
**How to avoid:** Use Tailwind v4 docs as reference. Key differences relevant here: `size-*` replaces `w-* h-*` for square elements, `shadow-*` unchanged, responsive prefixes unchanged (sm:/md:/lg:).
**Warning signs:** Classes not applying, missing styles.

### Pitfall 7: Content Collection Sorting
**What goes wrong:** Projects display in random/filesystem order.
**Why it happens:** getCollection returns entries in arbitrary order.
**How to avoid:** Sort explicitly: `projects.sort((a, b) => a.data.order - b.data.order)` and separate featured vs non-featured with `.filter()`.
**Warning signs:** Mercora and RecompAI appearing in wrong order.

## Code Examples

### Reusable Card Component (COMP-01)
```astro
---
interface Props {
  variant?: "default" | "featured";
  class?: string;
}

const { variant = "default", class: className } = Astro.props;
---

<div
  class:list={[
    "rounded-lg border border-white/10 bg-white/5 p-6",
    { "p-8 md:col-span-2": variant === "featured" },
    className,
  ]}
>
  <slot />
</div>
```

### Reusable Badge Component (COMP-02)
```astro
---
interface Props {
  variant?: "tech" | "status";
  class?: string;
}

const { variant = "tech", class: className } = Astro.props;
---

<span
  class:list={[
    "inline-flex items-center rounded px-2 py-0.5 text-xs font-medium",
    {
      "bg-accent/20 text-accent": variant === "tech",
      "bg-emerald-500/20 text-emerald-400": variant === "status",
    },
    className,
  ]}
>
  <slot />
</span>
```

### Section Header Component (COMP-03)
```astro
---
interface Props {
  title: string;
  subtitle?: string;
}

const { title, subtitle } = Astro.props;
---

<div class="mb-12">
  <h2 class="text-2xl font-semibold text-foreground md:text-3xl">{title}</h2>
  {subtitle && <p class="mt-2 text-muted">{subtitle}</p>}
</div>
```

### Index Page Composition
```astro
---
import Layout from "../layouts/Layout.astro";
import Hero from "../components/sections/Hero.astro";
import About from "../components/sections/About.astro";
import Projects from "../components/sections/Projects.astro";
import Career from "../components/sections/Career.astro";
import Writing from "../components/sections/Writing.astro";
import Contact from "../components/sections/Contact.astro";
---

<Layout title="Russell Moore — Engineering Leader & Builder">
  <Hero />
  <About />
  <Projects />
  <Career />
  <Writing />
  <Contact />
</Layout>
```

### Career Timeline Entry Pattern
```astro
<div class="relative border-l-2 border-white/10 pl-8">
  <div class="absolute -left-[9px] top-1 size-4 rounded-full border-2 border-accent bg-background"></div>
  <h3 class="font-semibold text-foreground">Company Name</h3>
  <p class="text-sm text-muted">Role Title</p>
  <ul class="mt-2 space-y-1 text-sm text-muted">
    <li>Key metric or achievement</li>
  </ul>
</div>
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| scroll event + debounce | IntersectionObserver | Widely supported since 2020 | Zero jank, better battery life |
| JS smooth scroll libraries | CSS scroll-behavior: smooth | Full support 2022+ | Zero JS needed for anchor scrolling |
| @astrojs/tailwind integration | @tailwindcss/vite direct plugin | Astro 5.2 (2025) | Simpler config, Tailwind v4 native |
| astro-icon v0 imports | astro-icon v1 from astro-icon/components | 2024 | New import path, data-icon attribute |
| Tailwind v3 JS config | Tailwind v4 CSS-first @theme | 2025 | No tailwind.config.js, CSS-native tokens |

## Open Questions

1. **Exact hero tagline copy**
   - What we know: Impact-focused, "Building products at the intersection of AI and commerce" style
   - What's unclear: Final wording
   - Recommendation: Use the example from D-01 as starting point, can be refined post-implementation

2. **About section narrative copy**
   - What we know: First-person, direct, confident, approachable
   - What's unclear: Exact paragraph text
   - Recommendation: Draft during implementation, iterate. Focus on AI + commerce + leadership angle

3. **Career entry details**
   - What we know: AT&T, Spark::red, Pivotree with quantified metrics
   - What's unclear: Exact roles, dates, metrics for each company
   - Recommendation: Use placeholder metrics initially, refine with Russell's real data. Stats from D-10 (20+ Years, $2B+, $13M ARR, 200+ team) provide the scale

4. **Active nav link highlighting**
   - What we know: User left this to Claude's discretion
   - What's unclear: Whether to implement scroll-spy or keep it simple
   - Recommendation: Add IntersectionObserver-based active highlighting. Each section observed, active link gets text-foreground vs text-muted treatment. Low complexity addition on top of existing observer pattern

## Sources

### Primary (HIGH confidence)
- [astro-icon Getting Started](https://www.astroicon.dev/getting-started/) - Installation, config, Icon component usage
- [astro-icon Customization](https://www.astroicon.dev/guides/customization/) - Iconify icon set integration pattern
- [Tailwind CSS scroll-behavior docs](https://tailwindcss.com/docs/scroll-behavior) - scroll-behavior utilities
- Existing codebase (src/layouts/Layout.astro, src/styles/global.css, src/content.config.ts) - Phase 1 patterns

### Secondary (MEDIUM confidence)
- [CSS mesh gradient patterns](https://medium.com/design-bootcamp/bringing-life-to-your-website-with-moving-mesh-gradient-backgrounds-20b7e26844a2) - Mesh gradient implementation approaches
- [IntersectionObserver sticky nav pattern](https://gist.github.com/tusamni/6227c01f0e876b9eee810a48aaea7aaa) - Sentinel-based scroll detection

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - astro-icon and @iconify-json/lucide verified on npm, versions confirmed
- Architecture: HIGH - Astro component patterns are well-documented, all patterns use standard Astro/Tailwind/vanilla JS
- Pitfalls: HIGH - Based on known Astro behaviors (script deduplication) and standard web platform issues (scroll-padding, body overflow)
- CSS animations: MEDIUM - Mesh gradient approach is standard CSS but exact values need visual tuning

**Research date:** 2026-03-26
**Valid until:** 2026-04-26 (stable stack, no breaking changes expected)
