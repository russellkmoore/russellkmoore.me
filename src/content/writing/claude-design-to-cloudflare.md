---
title: "From Claude Design to Live Site in Four Commands"
description: "Claude's artifact sandbox is a great design tool, just with some deployment limits. Here's the exact setup that gets you from mockup to a real URL — with push-to-deploy — without paying the build toolchain tax every time."
pubDate: 2026-07-12
tags: ["Cloudflare Workers", "AI", "React", "Tailwind", "Tooling"]
draft: false
---

## A Gap Between Design and Deploy

Claude Design is genuinely useful for building UI. You describe what you want, iterate in the artifact sandbox, and end up with a working React component in a fraction of the time it would take to write it from scratch. Sometimes a well-desinged microsite is all you need, right? Portfolio page, etc...

But then, ok, what do I do with it now?

The artifact sandbox has real restrictions: no `localStorage`, limited Tailwind support, no way to install packages. These are intentional — the sandbox runs in a shared browser context with security constraints. But they mean the thing you designed and the thing you can actually deploy are sadly two different things.

Getting from one to the other requires a build setup, and build setup for a fresh project can be annoying to get right:

- Vite + React needs the right config for Tailwind v4 (no more `tailwind.config.js` — it's CSS-first now, and most tutorials haven't caught up)
- Cloudflare Workers static asset serving requires a specific `wrangler.jsonc` structure
- GitHub Actions push-to-deploy requires a specific API token scope that the Cloudflare dashboard makes easy to get wrong

None of this is hard once you've done it, but it's a tax you pay again on every new project, and it has nothing to do with the thing you actually built.

## The Template

I published [claude-design-cloudflare-starter](https://github.com/russellkmoore/claude-design-cloudflare-starter) to handle it once.

The whole integration is one file. You clone the repo, run a setup script to name your project, paste your Claude Design component into `src/Portfolio.jsx` (keeping the `export default`), and deploy:

```bash
./setup.sh my-portfolio   # rename worker + package, install deps, init git
npm run dev               # preview at localhost:5173
npm run deploy            # build + deploy → your-name.workers.dev
```

Four commands from clone to live URL.

## What's Already Handled

**Tailwind v4 with no config file.** The CSS-first setup (`@import "tailwindcss"` in `src/index.css`, custom tokens under `@theme { ... }`) is already wired. Arbitrary values, custom colors, everything the sandbox restricts — it all works. This is the part that trips up most from-scratch setups because v4 dropped the JS config file entirely and the ecosystem docs are mixed.

**Full browser APIs.** `localStorage`, `sessionStorage`, fetch — no sandbox limits. If your Claude Design component stores state or makes API calls, it works exactly as you'd expect in a real deploy.

**Cloudflare Workers static asset serving.** `wrangler.jsonc` is already configured to serve `./dist`. You don't touch the deployment config unless you need a custom domain (the README covers that too — it's two clicks in the Cloudflare dashboard once your domain's zone is there).

**Push-to-deploy via GitHub Actions.** The workflow file is included. Add two repository secrets — a Cloudflare API token and your account ID — and every push to `main` ships automatically. The README calls out the exact token template to use in the Cloudflare dashboard; using the wrong one authenticates successfully but silently fails to deploy, which is a frustrating place to get stuck.

## The Porting Notes Worth Knowing

When you drop your Claude Design component into `src/Portfolio.jsx`, most things just work. A few things to know:

**Packages the component imports need to be installed.** If your design uses `lucide-react` or `recharts`, run `npm i lucide-react recharts` before `npm run dev`. The template ships with nothing pre-installed beyond React and Tailwind because there's no way to predict what your design needs.

**Custom colors and fonts go in `src/index.css`.** The `@theme` block is where you define tokens like `--color-brand: oklch(...)` that then work as utility classes (`text-brand`, `bg-brand`). This is Tailwind v4's equivalent of the `extend.colors` section in the old config file.

**Plain HTML export? Skip the build entirely.** If your Claude Design output is a self-contained `.html` file with no React or external dependencies, you don't need Vite at all. Drop the file in `public/`, change `assets.directory` in `wrangler.jsonc` from `./dist` to `./public`, and run `npx wrangler deploy`. Live in seconds.

## Why Cloudflare Workers

The obvious alternative for a static React site is Netlify or Vercel — both have one-click GitHub integrations and generous free tiers. For a design mockup or portfolio, any of these work.

Cloudflare Workers is worth knowing for two reasons beyond static hosting. First, when your site needs a backend — an API route, edge logic, a database — it's the same deploy target. You don't add a service; you add a handler. The cost model and the operational complexity stay flat as the project grows. Second, Cloudflare recently acquired Astro, which makes it the strongest native target for Astro-based projects (like this portfolio site).

The starter keeps the `wrangler.jsonc` setup as simple as it gets while still being production-appropriate: static assets from `./dist`, worker name set by the setup script, no Durable Objects or KV bindings unless you add them.

## Use It As a Template

The GitHub repo is set up as a template repository. "Use this template" creates a fresh copy under your account with a clean git history — the right way to start a new project from it rather than cloning and clearing history manually.

MIT licensed. Fork it, extend it, swap Cloudflare for something else if you want — the build setup is standard Vite and Tailwind regardless of where you deploy.

[github.com/russellkmoore/claude-design-cloudflare-starter](https://github.com/russellkmoore/claude-design-cloudflare-starter)
