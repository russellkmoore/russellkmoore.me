New open-spurce template this week: Claude Design → Cloudflare Starter.

Claude's artifact sandbox is great for designing UI fast, but it has real limits — no localStorage, restricted Tailwind, no deploy target. Getting from mockup to a live URL means setting up a build pipeline that has nothing to do with what you built.

The template absorbs that tax. Four commands:

```
./setup.sh my-portfolio   # name your worker, install deps
npm run dev               # preview at localhost:5173
npm run deploy            # ships to your-name.workers.dev
```

What's already handled:

→ Tailwind v4 CSS-first config (no tailwind.config.js — most tutorials haven't caught up to this yet)
→ Cloudflare Workers static asset serving via wrangler.jsonc
→ GitHub Actions push-to-deploy — add two secrets, every push to main ships automatically
→ Full browser APIs — localStorage works, no sandbox restrictions
→ Plain HTML escape hatch — if your export is a self-contained .html file, skip the build entirely

The only thing you touch is src/Portfolio.jsx. Paste your Claude Design component in, keep the export default, run deploy.

It's also a GitHub template repository, so "Use this template" gives you a fresh copy with clean history — the right way to start a new project from it.

MIT licensed.

→ github.com/russellkmoore/claude-design-cloudflare-starter
→ Write-up: russellkmoore.me/writing/claude-design-to-cloudflare

#OpenSource #CloudflareWorkers #React #Tailwind #AI
