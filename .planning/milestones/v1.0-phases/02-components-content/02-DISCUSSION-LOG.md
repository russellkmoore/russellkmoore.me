# Phase 2: Components & Content - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-03-26
**Phase:** 02-components-content
**Areas discussed:** Hero & first impression, Section flow & navigation, Content presentation, Responsive strategy, Section dividers & spacing, Footer content & style, About section narrative

---

## Hero & First Impression

| Option | Description | Selected |
|--------|-------------|----------|
| Technical leadership | "Engineering Leader & Builder" — direct, no-nonsense | |
| Impact-focused | "Building products at the intersection of AI and commerce" — specific to what you do | ✓ |
| Dual-identity | "Engineering executive by day, AI product builder by night" — shows both sides | |

**User's choice:** Impact-focused
**Notes:** Tagline should be specific to Russell's work, not generic title

---

| Option | Description | Selected |
|--------|-------------|----------|
| Subtle mesh gradient | Slow-moving violet + dark blue blobs behind text. Vercel.com energy | ✓ |
| Gradient border/glow | No background gradient, subtle animated violet glow around hero section | |
| You decide | Claude picks | |

**User's choice:** Subtle mesh gradient

---

| Option | Description | Selected |
|--------|-------------|----------|
| Primary + ghost | Solid violet + ghost outline buttons | |
| Both solid, different weight | Both filled, different colors | |
| Text links with arrows | No buttons — styled text links with arrow icons. Linear.app energy | ✓ |

**User's choice:** Text links with arrows

---

| Option | Description | Selected |
|--------|-------------|----------|
| Full viewport | 100vh hero, must scroll to see content | |
| Generous but not full | ~60-70vh, next section peeks above fold | ✓ |
| Compact | Just enough space, content starts quickly | |

**User's choice:** Generous but not full (~60-70vh)

---

## Section Flow & Navigation

| Option | Description | Selected |
|--------|-------------|----------|
| Hero → About → Projects → Career → Writing → Contact | Story flow | ✓ |
| Hero → Projects → About → Career → Writing → Contact | Work-first | |
| Hero → Projects → Career → About → Writing → Contact | Resume flow | |

**User's choice:** Story flow (Hero → About → Projects → Career → Writing → Contact)

---

| Option | Description | Selected |
|--------|-------------|----------|
| Always visible | Sticks from start, solid background | |
| Appears on scroll | Hidden in hero, slides in after scroll | |
| Transparent → solid | Always visible, transparent over hero, solid on scroll | ✓ |

**User's choice:** Transparent → solid

---

| Option | Description | Selected |
|--------|-------------|----------|
| Section links only | About, Projects, Career, Writing, Contact text links | ✓ |
| Section links + resume CTA | Same links plus Resume/CV button | |
| Minimal — logo + hamburger only | Just name and hamburger even on desktop | |

**User's choice:** Section links only

---

| Option | Description | Selected |
|--------|-------------|----------|
| Smooth scroll | CSS scroll-behavior: smooth | ✓ |
| Instant jump | Standard anchor behavior | |
| You decide | Claude picks | |

**User's choice:** Smooth scroll

---

## Content Presentation

| Option | Description | Selected |
|--------|-------------|----------|
| Full cards with hover | Rounded cards, border, hover lift/glow. 2-column grid | |
| Minimal cards, no border | Clean blocks with spacing only. Vercel.com style | |
| Featured card + list | Featured projects get larger cards, non-featured in compact list | ✓ |

**User's choice:** Featured card + list

---

| Option | Description | Selected |
|--------|-------------|----------|
| Paragraph above, stats below | Narrative text first, then 4-column stats row | ✓ |
| Stats grid with paragraph aside | 2-column: paragraph left, stats right | |
| Stats first, paragraph after | Lead with numbers, then explain | |

**User's choice:** Paragraph above, stats below

---

| Option | Description | Selected |
|--------|-------------|----------|
| Timeline style | Vertical timeline with connected dots/line | ✓ |
| Cards grid | 3 cards in a row | |
| Stacked blocks | Full-width blocks stacked vertically | |

**User's choice:** Timeline style

---

| Option | Description | Selected |
|--------|-------------|----------|
| Centered CTA block | Brief paragraph centered, links with arrows below | ✓ |
| Two-column | Copy left, links right | |
| Minimal footer merge | Contact in footer instead of separate section | |

**User's choice:** Centered CTA block

---

## Section Dividers & Spacing

| Option | Description | Selected |
|--------|-------------|----------|
| Whitespace only | Generous padding, no lines. Linear.app style | |
| Subtle border lines | Thin white/10 opacity lines | |
| Gradient fade dividers | Soft gradient fades (dark → lighter → dark) | ✓ |

**User's choice:** Gradient fade dividers

---

## Footer Content & Style

| Option | Description | Selected |
|--------|-------------|----------|
| Single centered line | Copyright centered with inline icon links | |
| Two-column footer | Copyright left, icons right | |
| You decide | Claude picks most minimal approach | ✓ |

**User's choice:** Claude's discretion

---

## About Section Narrative

| Option | Description | Selected |
|--------|-------------|----------|
| First-person, direct | "I build AI-powered products..." Personal, confident | ✓ |
| Third-person, professional | "Russell Moore is an engineering leader..." Formal | |
| First-person, narrative | "I've spent 20 years..." Storytelling, longer | |

**User's choice:** First-person, direct

---

## Claude's Discretion

- Footer layout (minimal, satisfying LAYOUT-03)
- Grid breakpoint behavior per section (mobile stacking)
- Exact gradient animation CSS
- Section heading component details
- Badge and Card component styling
- Active nav link highlighting

## Deferred Ideas

None — discussion stayed within phase scope
