---
title: "Five Models, Five Jobs: Multi-Model Orchestration on Cloudflare Workers"
description: "Why a single model can't do everything well, and how I route across five different models based on task requirements in a production AI application."
pubDate: 2026-03-24
tags: ["AI", "Architecture", "Cloudflare Workers"]
draft: false
---

## The Instinct Is Wrong

The default approach to building with LLMs is to pick the best model you can afford and use it for everything. It's simple, easy to reason about, and wrong for anything beyond a chatbot.

I learned this the hard way building RecompAI — an AI-powered coaching platform running entirely on Cloudflare Workers AI. The app handles intent classification, food photo analysis, tool-calling workflows, knowledge synthesis, and conversational coaching. Using a single model for all of these tasks means you're either paying too much for simple classification, getting unreliable results on complex tool calling, or both.

RecompAI now uses five different models, each selected for a specific capability. The routing logic that connects them is straightforward, but getting to this architecture involved some painful lessons about what models are actually good at.

## The Five Models

**Llama 3.1 8B — Intent Router**

Every incoming message first passes through a lightweight 8B model that classifies it into one of four domains: meal, protocol, body-metrics+workout, or general. The model outputs a JSON object with a domain and confidence score, capped at 60 tokens. This classification takes milliseconds and costs almost nothing.

Why not use the big model for this? Because classification doesn't need reasoning ability — it needs speed and consistency. The 8B model classifies "I had chicken for lunch" as `meal` with 0.95 confidence just as accurately as a 70B model would, in a fraction of the time. It also handles onboarding Q&A, where the conversation is simple enough that tool calling isn't needed.

```typescript
export const AI_MODEL_ROUTER = '@cf/meta/llama-3.1-8b-instruct';

// Router prompt produces constrained JSON output
// {"domain": "meal", "confidence": 0.95}
```

**Llama 3.2 11B Vision — Food Photo Analysis**

When a user uploads a food photo, the system needs a multi-modal model that can identify food items, estimate portions, and return structured macro data. This is the only model in the stack that processes images. It fires exclusively when the request includes an image — every other path uses a text-only model.

Using a vision model for text-only requests would be wasteful. Using a text-only model for image requests would be impossible. The routing here is binary and obvious, but the broader principle matters: match the model's capability to the task's requirement.

**Llama 4 Scout 17B — Primary Specialist**

This is the workhorse for tool-calling workflows. When the router classifies a message as high-confidence single-domain, it routes to a specialist agent with scoped tools and a domain-specific system prompt. Scout handles the actual work: searching the USDA database, logging meals, managing supplement protocols, recording workouts.

Scout was promoted to primary after I tested it against Llama 3.1 70B. The larger model had two problems: nondeterministic tool sequencing (it would randomly reorder multi-step tool calls between identical requests) and weak hypothetical rejection (it would log data on "what if" questions despite explicit prompt instructions). Scout is faster, cheaper, and more reliable at following structured tool-calling instructions. Bigger is not always better for constrained tasks.

**GLM 4.7 Flash — Fallback**

When the primary model is unavailable or returns errors, the system falls back to GLM 4.7 Flash. Having a fallback model is a production necessity, not a luxury. Workers AI models go through updates and occasionally have degraded availability. A fallback path means users get a response — maybe not the best response, but better than an error message.

**BGE Base (bge-base-en-v1.5) — Embeddings**

The embedding model generates 768-dimensional vectors for semantic food search. User queries and USDA food descriptions are embedded into the same vector space, enabling natural language food lookup ("something high in protein for a quick lunch") rather than exact keyword matching.

This model runs in parallel with text generation — the search embedding happens concurrently with context assembly, so it doesn't add to the overall latency of a request.

## The Routing Architecture

The routing decision tree is simple:

1. **Image present?** → Vision model, route to meal specialist.
2. **Text only?** → Run through the 8B router for domain classification.
3. **High confidence, single domain?** → Route to domain specialist with scoped tools.
4. **Low confidence or multi-domain?** → Route to coordinator, which fans out to specialists in parallel and synthesizes.

The coordinator is important. When a user says "I had a protein shake after my workout, did I hit my protein goal?" — that touches the meal domain, the workout domain, and possibly the body metrics domain. The coordinator dispatches to relevant specialists in parallel via `Promise.allSettled`, collects their responses, and synthesizes into one coherent coaching response.

The coordinator calls no domain tools itself. This is a deliberate architectural constraint. If the coordinator could call tools directly, it would have access to all 20+ tools and suffer the same reliability problems as the monolithic architecture we replaced. By delegating tool calling to scoped specialists, each agent only sees the 4-6 tools relevant to its domain.

## Environment Variable Model Overrides

Every model assignment can be overridden via environment variables without a code deploy:

```typescript
export function getModelConfig(
  task: 'router' | 'specialist' | 'vision' | 'onboarding' | 'summary',
  env: Partial<Pick<Env, 'MODEL_ROUTER' | 'MODEL_SPECIALIST' | ...>>,
  domain?: string
): ModelConfig {
  switch (task) {
    case 'router':
      return { model: env.MODEL_ROUTER ?? AI_MODEL_ROUTER, max_tokens: 60 };
    case 'specialist':
      if (domain === 'protocol' && env.MODEL_SPECIALIST_PROTOCOL)
        return { model: env.MODEL_SPECIALIST_PROTOCOL };
      return { model: env.MODEL_SPECIALIST ?? AI_MODEL_TEXT };
    // ...
  }
}
```

This matters more than it seems. When Cloudflare releases a new model or updates an existing one, I can swap it into a specific task slot via `wrangler secret put` and test without touching the codebase. Per-domain overrides let me run a different model for protocol specialist versus meal specialist if one model handles a particular domain better.

## What I'd Do Differently

**Start with the router earlier.** I built the multi-model architecture in v3.0, after months of fighting with a single-model approach. The router-to-specialist pattern should have been the design from the start. The monolithic model with 20+ tools was never going to be reliable.

**Invest in eval before swapping models.** I built a Claude-as-judge evaluation framework that runs test cases against the full dispatch pipeline — checking routing accuracy, required and forbidden tool calls, and response quality on a weighted rubric. This should have existed before I started testing different models, not after. Without automated eval, model comparisons are vibes-based, and vibes don't catch regressions.

**Don't trust model size as a proxy for capability.** The 70B model being worse than Scout at tool calling was genuinely surprising. The lesson is that model capability is task-specific. A model that writes better prose might be worse at following structured instructions. The only way to know is to test on your actual workload.

## The Takeaway

Multi-model orchestration isn't about using the most models — it's about matching model capabilities to task requirements. A cheap, fast model for classification. A specialized model for vision. A reliable model for tool calling. A fallback for availability. Each model does one job well, and the routing layer connects them.

The total inference cost is lower than using a single large model for everything, the reliability is higher because each model operates within its strengths, and the architecture is flexible enough to swap models independently as better options emerge.
