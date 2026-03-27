# Phase 3: Polish & Production - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-03-26
**Phase:** 03-polish-production
**Areas discussed:** OG image strategy, Hero motion enhancement

---

## OG Image Strategy

| Option | Description | Selected |
|--------|-------------|----------|
| Static designed image | 1200x630 PNG with name/tagline/branding. Committed to public/ | |
| Screenshot-based | Styled screenshot of hero section. Most accurate to site | ✓ |
| SVG with text | Programmatic SVG. Lightweight but some platforms don't render SVG for OG | |

**User's choice:** Screenshot-based
**Notes:** Captures actual visual impact of the hero section

---

## Hero Motion Enhancement

| Option | Description | Selected |
|--------|-------------|----------|
| Faster + larger gradient | Speed up to 8-12s, increase blob size/opacity | ✓ |
| Text entrance animation | Fade-in + slide-up for name/tagline/CTAs on load | |
| Both — gradient + entrance | Faster gradient AND text entrance | |

**User's choice:** Faster + larger gradient
**Notes:** Addresses Phase 2 checkpoint feedback "hero could have a little more motion"

---

## Claude's Discretion

- Accessibility (heading hierarchy, ARIA labels, keyboard nav, skip-to-content, focus-visible)
- Sitemap generation
- Additional Lighthouse optimizations

## Deferred Ideas

None — discussion stayed within phase scope
