# russellkmoore.me

Personal portfolio site for Russell Moore -- engineering leader, builder, and AI product creator.

Built with Astro 5, Tailwind CSS v4, and deployed to Cloudflare Workers.

## Development

### Prerequisites

- Node.js 18+
- npm 9+

### Setup

```bash
git clone <repo-url>
cd russellkmoore.me
npm install
```

### Commands

| Command | Action |
|---------|--------|
| `npm run dev` | Start local dev server |
| `npm run build` | Build static site to `dist/` |
| `npm run preview` | Preview built site locally |
| `npm run deploy` | Build and deploy to Cloudflare Workers |
| `npm run check` | Run Astro type checking |

## Deployment

Deployed to Cloudflare Workers via wrangler.

```bash
npm run deploy
```

This runs `astro build` followed by `wrangler deploy`, pushing the static `dist/` directory to Cloudflare's edge network.

### First-time deploy

1. Run `npx wrangler login` to authenticate with Cloudflare
2. Run `npm run deploy`

## Tech Stack

- **Framework:** Astro 5 (static output)
- **Styling:** Tailwind CSS v4 (CSS-first config)
- **Font:** Geist Sans (variable) + Inter (fallback)
- **Deployment:** Cloudflare Workers
