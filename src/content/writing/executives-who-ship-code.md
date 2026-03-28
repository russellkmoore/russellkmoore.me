---
title: "The Case for Executives Who Still Ship Code"
description: "Why hands-on technical work makes you a better leader, not a worse one — and how the industry got this backwards."
pubDate: 2026-03-23
tags: ["Leadership", "Engineering Culture"]
draft: false
---

## The Conventional Wisdom Is Comfortable

Somewhere around the Director level, most technology leaders stop writing code. The reasoning is sensible on the surface: your time is more valuable in meetings, your impact is through the people you manage, and staying in the code sends the signal that you don't trust your team. The advice is consistent across leadership books, executive coaches, and well-meaning mentors. Let go. Delegate. Rise above the details.

I've led teams of 200+ engineers across three continents, managed a $30M P&L, navigated an acquisition and an IPO. I've done the executive thing. And I think the conventional wisdom is wrong — not about delegation, which is essential, but about the assumption that technical depth and leadership effectiveness are in tension.

They're not. They're complementary. And the gap between leaders who build and leaders who don't is widening as the technology landscape accelerates.

## What You Lose When You Stop

The most dangerous failure mode for a technical leader isn't micromanagement. It's losing calibration.

When you stop building, you lose the ability to evaluate the difficulty of what your team is doing. You become dependent on your team's own estimates, which are subject to all the biases that estimates have always been subject to. A task described as "complex" might be straightforward. A task described as "a quick fix" might touch a critical path. Without hands-on experience with current tools and patterns, you can't tell the difference.

This calibration loss compounds over time. After a year away from the code, you're relying on pattern matching from an outdated mental model. After two years, you're making architectural decisions based on how things worked when you last touched them. After five years, you're the executive that engineers privately joke about — the one who asks why the team can't "just add a button for that."

I've been in rooms where a CTO confidently stated that a migration should take two sprints, and the team knew it was a quarter-long effort. The CTO wasn't stupid — they were uncalibrated. They hadn't felt the weight of a migration in years.

## What You Gain When You Don't Stop

The projects I build on nights and weekends — RecompAI, Mercora, the other experiments in my portfolio — aren't consulting deliverables or startup side hustles. They're how I stay current. And "current" means something specific: I'm not reading about multi-model orchestration and tool-calling guardrails and edge computing. I'm debugging them at midnight when the Llama model narrates instead of calling tools for the third time.

That hands-on experience changes how I lead in concrete ways.

**I can evaluate AI vendor claims.** When a vendor pitches "seamless AI integration," I know what the tool-calling failure modes actually look like, what guardrails you need that they won't mention, and how much engineering work hides behind the word "seamless." When a team tells me a model swap will take a sprint, I know whether that's reasonable or whether they're underestimating the eval work.

**I can have real architecture conversations.** Not the kind where the leader nods along and asks generic questions about scalability. The kind where we debate whether the coordinator agent should have direct tool access or delegate to specialists, and I have an informed opinion because I've built both patterns and know the tradeoffs.

**I can spot when the team is stuck.** There's a specific body language engineers have when they're fighting a framework rather than solving a problem. I recognize it because I've been there recently, not because I remember being there a decade ago.

**I earn a kind of trust that title alone doesn't provide.** Engineers know the difference between a leader who says "I understand the technical challenges" and one who actually does. You can't fake the specificity that comes from recently dealing with Workers AI's default 256-token limit silently truncating your model's output.

## The Time Objection

The immediate pushback is always about time. You're a VP — you don't have time to write code. You have board decks and strategy sessions and one-on-ones and quarterly planning.

This is true. It's also a false dichotomy. I'm not arguing that executives should be writing production code for their companies. I'm arguing that they should be building things — side projects, prototypes, experiments — that keep their technical intuition alive. The time investment is real, but it's measured in hours per week, not hours per day. It's the same investment that a doctor makes by reading journals or a lawyer makes by following case law. It's professional maintenance.

The time you spend staying technical pays back in the time you don't spend being wrong about technical decisions. Every miscalibrated estimate, every architecture decision based on stale mental models, every vendor evaluation that misses the real tradeoffs — those cost weeks, not hours.

## The Signal Problem

There's a subtler version of the objection: coding sends the wrong signal. It says you don't trust your team. It says you're not operating at the right altitude.

In my experience, the signal depends entirely on how you do it. If you're rewriting your team's code or inserting yourself into their sprints, yes — that's micromanagement and it undermines trust. But if you're building your own projects, exploring new technology, and bringing genuine technical insight into architectural discussions, the signal is the opposite. It says you care about the craft, you respect the difficulty of the work, and your opinions are earned rather than inherited from your last hands-on role.

The best engineers I've worked with — the ones I want on my team and the ones I want to work for — are energized by leaders who build. They're wary of leaders who stopped.

## The Acceleration Argument

This has always been true to some extent, but the current moment makes it urgent. AI, edge computing, serverless architectures, new deployment models — the technology landscape is shifting faster than at any point in my career. The gap between a leader who's building with these tools and one who's reading about them in analyst reports is wider than it's ever been.

A year ago, I hadn't built a multi-agent AI system with custom tool-calling guardrails. I hadn't implemented semantic search with vector embeddings on edge infrastructure. I hadn't dealt with the real production challenges of running LLM inference on Cloudflare Workers. Now I have, and that experience directly informs how I evaluate opportunities, hire engineers, and make architectural decisions.

That's not despite being a leader. It's part of being one.
