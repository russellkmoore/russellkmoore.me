# Phase 5: Writing/Blog System - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.

**Date:** 2026-03-27
**Phase:** 05-writing-blog-system
**Areas discussed:** Post page layout, Content format, Homepage writing section

---

## Post Page Layout

| Option | Description | Selected |
|--------|-------------|----------|
| Clean article page | Centered column, title + date + tags, minimal | |
| Article + sidebar | Content left, TOC/related posts sidebar right | |
| Full-width immersive | Hero gradient/image spanning full width, centered content below | ✓ |

**User's choice:** Full-width immersive

---

| Option | Description | Selected |
|--------|-------------|----------|
| Yes — show reading time | "5 min read" next to date | |
| No — keep it clean | Just date and tags | ✓ |

**User's choice:** No reading time

---

## Content Format

| Option | Description | Selected |
|--------|-------------|----------|
| MDX (Recommended) | Markdown + embedded components, requires @astrojs/mdx | ✓ |
| Plain Markdown | Standard markdown, no component embeds | |

**User's choice:** MDX

---

| Option | Description | Selected |
|--------|-------------|----------|
| Yes — with dark theme | Shiki syntax highlighting matching dark theme | ✓ |
| No — not needed | No code snippets | |

**User's choice:** Yes, dark theme syntax highlighting

---

## Homepage Writing Section

| Option | Description | Selected |
|--------|-------------|----------|
| Latest 3 | 3 most recent, "View all" link | ✓ |
| Latest 6 | 6 posts in 2x3 grid | |
| Latest 2 featured | Only featured posts | |

**User's choice:** Latest 3

---

| Option | Description | Selected |
|--------|-------------|----------|
| Card is the link | Entire card clickable | |
| "Read more" text link | Explicit link at bottom | |
| Both | Card clickable AND "Read more →" link | ✓ |

**User's choice:** Both

---

## Claude's Discretion

- Post page hero gradient approach
- Writing index page layout
- Post navigation (prev/next)
- Prose typography styles
- Back navigation

## Deferred Ideas

- RSS feed (V2-01)
- Reading time estimate
- Tag filtering page
- Related posts
- Social share buttons
