New Black Magic Consulting build — live today.

The Invest Collective is a members-only platform for a private trading group. Research library, signup flow, analytics dashboard — the usual. The interesting piece is the Thesis Tracker.

Most investing communities celebrate the calls they got right and quietly bury the ones they didn't. We built the opposite: every thesis ships with a bounded timeframe, 2–8 measurable data points, and a public outcome score when it closes. Wins and losses, both visible. That's the product.

The other half is how we got there without a $500–$5K/mo Bloomberg + Airflow + warehouse pipeline:

→ ~600-line stateless MCP server gives Claude tools to draft theses and update data
→ Cowork nightly task runs on Anthropic's cloud after market close — pulls current values from Yahoo/FRED/MarketWatch, recomputes every active thesis, closes expired ones
→ Humans review every AI-drafted thesis before it ships. The API owns truth. AI is the draftsman.
→ Cloudflare Access gates /admin — zero custom auth code
→ Next.js 15 + D1 + R2 + Workers AI, one deploy target. Infra cost rounds to $0/mo.

I led the program end-to-end for our client: architecture, data model, API design, the MCP + Cowork workflow, and the rollout to production. This is the version of AI-assisted product delivery I keep pitching — ship small, keep humans on the loop, let automation handle the boring middle.

Live: https://theinvestcollective.com
Case study: https://russellkmoore.me/projects/invest-collective

#ProductLaunch #AI #MCP #CloudflareWorkers #FinTech
