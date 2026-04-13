Claude is the best coding assistant I've used. It's also wildly overqualified for half the work I give it.

Scaffolding tests. Generating docs. Writing commit messages. Reformatting code. These are mechanical tasks that don't need frontier-model reasoning — they need a model that can follow a spec and type fast.

So I built CF Code Assistant: a 12-tool MCP server on Cloudflare Workers that routes the mechanical work to Workers AI while Claude keeps doing what Claude is good at — reasoning, architecture, judgment.

I built it while using GSD (https://github.com/gsd-build/get-shit-done) for spec-driven design. The plan/execute/verify loop produces excellent results, but it burns through tokens fast — and that cost pressure is exactly what made me want to offload the mechanical work to a cheaper model.

The setup:
→ Two model tiers (fast for quick tasks, standard for generation)
→ Hot-swappable models via KV — no redeploy when a better model ships
→ OAuth 2.1 auth with rate limiting
→ 108 tests, 95.5% statement coverage
→ 760 lines, single file, fully auditable

Claude still drives. It just stops paying Opus rates to write boilerplate code.

Open source: https://github.com/russellkmoore/cf-code-assistant

I also wrote up the full build story — what it actually took to go from MCP tutorial to production server (auth, testing, observability, and every decision nobody covers in the docs): https://russellkmoore.me/writing/building-production-mcp-server

#MCP #CloudflareWorkers #AI #DeveloperTools #OpenSource
