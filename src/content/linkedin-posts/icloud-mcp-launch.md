I have an old-school .mac email address. No surprises with anyone that knows me; I am kinda an apple fanboy, and have been for decades.

Claude can now read my iCloud inbox, search my iCloud calendars, and tee up a rough draft for me to rework. Now, I don't let it *send* email... so there is no send code for it to misuse. Making all of that safe took more forethought than just making it work.

I just finished up building iCloud MCP: an MCP server on Cloudflare Workers that speaks IMAP, CalDAV, and CardDAV to iCloud directly. No Mac mini running AppleScript, no sync service holding my credentials. One Worker, one app-specific password that never leaves it. On my $5/mo Workers paid plan it basically runs free, no local compute resources.

The fun problem: no IMAP client on npm runs on Workers, so the project speaks the protocol itself over a raw TCP socket at the edge. As far as I could find, nobody had published proof that was even possible against iCloud. And boom goes the dynamite.

The real problem, however: an AI with your mailbox is a genuine security exercise. So:

→ No SMTP anywhere in the codebase. Claude roughs out a starting point and drops it in Drafts. I am the human in the middle who decides if it is in my voice and says what I am trying to say 
→ Calendar writes are preview-then-commit with signed, single-use confirmation tokens
→ Email and Calendar content is fenced as untrusted data (a stranger's meeting invite is a prompt injection waiting to happen)
→ Reading mail never marks it read
→ An 1,100-line source scanner enforces every safety rule on each commit
→ 1,900+ tests running in the real Workers runtime, not a Node mock

Took me fourteen days, spec-driven using gsd-core, built with Claude Opus to give Claude Cowork a better way to handle mail, contacts, and calendars for me.

Open source: https://github.com/russellkmoore/icloud-mcp

I wrote up the full story, including the safety engineering I now think is the actual point of personal MCP servers: https://russellkmoore.me/writing/giving-claude-my-inbox

#MCP #CloudflareWorkers #AI #OpenSource #Security
