# Phase 1: Foundation & Scaffolding - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-03-26
**Phase:** 01-foundation-scaffolding
**Areas discussed:** Cloudflare deployment, Content schema details

---

## Cloudflare Deployment

| Option | Description | Selected |
|--------|-------------|----------|
| Direct static deploy (Recommended) | No adapter. Build to dist/, wrangler.toml with assets.directory pointing at dist/. Simpler, no SSR runtime. | |
| Keep @astrojs/cloudflare | Use the adapter as originally specified, even though output is static. Adds unnecessary runtime layer. | |
| Cloudflare Pages instead | Use Cloudflare Pages (not Workers) for static hosting. Even simpler, but different deploy flow. | |

**User's choice:** Asked about SSR advantages first, then chose "SSR from the start"
**Notes:** User wanted to understand the SSR trade-off before deciding. After learning SSR enables dynamic routes, API routes, and auth at the edge — and that switching later is easy — they chose to install the adapter from day one for future-proofing. Output stays "static" for v1.

### Follow-up: SSR Trade-off

| Option | Description | Selected |
|--------|-------------|----------|
| Static now, SSR later (Recommended) | No adapter for v1. Easy to add later if needed. | |
| SSR from the start | Install @astrojs/cloudflare now. More setup but ready for dynamic features. | ✓ |

---

## Content Schema Details

### Status Field

| Option | Description | Selected |
|--------|-------------|----------|
| Two values | "Live" and "In Development" only | |
| Three values | "Live", "In Development", and "Archived" | ✓ |
| Free text | Any string — maximum flexibility | |

**User's choice:** Three values
**Notes:** Added "Archived" for future retired projects.

### Featured Field

| Option | Description | Selected |
|--------|-------------|----------|
| Boolean (Recommended) | featured: true/false — featured projects appear first | ✓ |
| Sort order number | featured: 1, 2, 3 — controls exact display order | |

**User's choice:** Boolean (Recommended)

### Tags Format

| Option | Description | Selected |
|--------|-------------|----------|
| Free-form strings | tags: ["AI", "Leadership", "Commerce"] — flexible | ✓ |
| Predefined enum | Restricted to a defined set of tags | |

**User's choice:** Free-form strings

---

## Claude's Discretion

- Exact wrangler.toml configuration details
- npm script implementation
- README structure and content depth
- TypeScript configuration
- Exact Tailwind v4 theme token syntax

## Deferred Ideas

None — discussion stayed within phase scope
