---
title: "What It Actually Takes to Build a Production MCP Server"
description: "Lessons from building CF Code Assistant — a 12-tool MCP server on Cloudflare Workers that routes code generation to cheaper models. Auth, testing, observability, and the decisions nobody talks about."
pubDate: 2026-04-13
tags: ["AI", "MCP", "Cloudflare Workers", "Architecture"]
draft: false
---

## MCP Tutorials Stop at "Hello World"

Every MCP tutorial follows the same pattern: define a tool schema, wire up a handler, connect it to Claude, and watch the magic happen. The demo takes ten minutes. The production server that follows takes weeks — and the gap between them is where all the real engineering lives.

I just shipped CF Code Assistant, a 12-tool MCP server running on Cloudflare Workers that offloads mechanical code generation from Claude to Cloudflare Workers AI. The idea is straightforward: Claude is expensive for boilerplate. Scaffolding tests, generating docs, writing commit messages, reformatting code — none of these require Claude's reasoning capability. Route them to a cheaper model and save the tokens.

The routing concept took an afternoon to prototype. The production-grade server took weeks. Here's what filled that gap.

## Decision 1: Stateless Per-Request vs. Durable Objects

The MCP SDK gives you two paths on Cloudflare Workers. You can use `McpAgent` with Durable Objects for persistent sessions, or `createMcpHandler` for stateless per-request handling. Most tutorials push you toward Durable Objects because they feel more natural — the MCP protocol is inherently session-oriented.

I went stateless. CF Code Assistant's tools are pure functions: code goes in, code comes out. There's no conversational state to maintain between tool calls, no user session that needs to persist. Every request creates a fresh `McpServer` instance, processes one tool call, and returns. This also sidesteps a CVE in MCP SDK 1.26.0 that affected shared server instances.

The tradeoff is that I can't do streaming responses or multi-turn tool sequences within a single MCP connection. For code generation tools, that's fine — the model generates the complete output in one shot. If I were building a conversational MCP server (one that maintained dialogue state across calls), Durable Objects would be the right choice. Match the infrastructure to the interaction pattern.

## Decision 2: Two-Tier Model Routing

Not all tools need the same model. A commit message from a diff is a simpler task than generating a full test suite from source code. Running the big model for both wastes money on the simple task and adds unnecessary latency.

CF Code Assistant splits tools into two tiers. The fast tier uses a lightweight model for quick tasks: `quickTask`, `generateCommitMessage`, `explainCode` at brief depth. The standard tier uses the full model for generation work: `generateCode`, `reviewCode`, `scaffoldTests`, `generateTypes`, and the rest.

The key design decision: both tiers resolve at runtime from KV, not from code. A KV key `config:model:fast` controls the fast tier model, `config:model:standard` controls the standard tier. When Cloudflare ships a better model — and they ship them regularly — I update a KV value. No code change, no redeploy, no downtime.

```typescript
async function resolveModel(
  tier: 'fast' | 'standard',
  kv: KVNamespace
): Promise<string> {
  const key = `config:model:${tier}`;
  const stored = await kv.get(key);
  if (stored && ALLOWED_MODELS.includes(stored)) return stored;
  return tier === 'fast' ? DEFAULT_FAST_MODEL : DEFAULT_STANDARD_MODEL;
}
```

The self-healing part matters: if someone writes a bad value to KV (typo in a model name, a model that got deprecated), the resolver falls back to the hardcoded default instead of crashing. The model allowlist prevents prompt injection from swapping in unauthorized models via KV manipulation.

## Decision 3: Auth That Doesn't Suck

MCP servers sit between your AI assistant and an inference backend. Without auth, anyone who discovers your Worker URL can run inference on your Cloudflare account. The `@cloudflare/workers-oauth-provider` package handles OAuth 2.1 with a PIN-based flow — you set a PIN as a Worker secret, and the first connection triggers a browser auth flow where you enter it. Subsequent connections use a cached token.

The implementation details that took real time:

**CSRF protection.** The auth form generates a CSRF token stored in KV with a 5-minute TTL. The form submission validates the token before checking the PIN. Without this, an attacker could craft a form that submits a PIN guess to your auth endpoint from another site.

**Rate limiting.** Five auth attempts per minute per IP. The rate limit uses Cloudflare's built-in rate limiting binding rather than a custom KV counter, so it's handled at the edge before the Worker even executes. After five failures, the response is a 429 with a clear message — not a silent rejection that leaves the user wondering what happened.

**Timing-safe comparison.** PIN comparison uses a constant-time check to prevent timing attacks. This is probably overkill for a personal MCP server, but it's a one-line change and it's the right habit.

```typescript
function timingSafeEqual(a: string, b: string): boolean {
  if (a.length !== b.length) return false;
  let result = 0;
  for (let i = 0; i < a.length; i++) {
    result |= a.charCodeAt(i) ^ b.charCodeAt(i);
  }
  return result === 0;
}
```

## Decision 4: Input Validation Before Inference

Every tool accepts user-provided code as input. Without size limits, a malicious or careless client could send megabytes of code to a model that has a fixed context window. The model would either truncate silently (producing bad output) or error out (wasting a round trip).

Every tool validates input size before the AI call. Code inputs cap at 100K characters, context at 50K, quick tasks at 10K. The limits are generous enough for real-world use and strict enough to prevent abuse. Validation errors return structured MCP error responses with the specific limit that was exceeded, so the caller knows exactly what to fix.

This is the kind of thing that doesn't exist in any MCP tutorial because tutorials don't have adversarial users. Production servers do.

## Decision 5: Error Handling That Protects Secrets

When an AI call fails — timeout, bad response format, model unavailable — the error message goes back to the MCP client, which in most cases is Claude. Claude will display that error to the user and potentially reason about it. If your error message contains a stack trace with file paths, environment variable names, or KV namespace IDs, you've just leaked infrastructure details into a conversation.

CF Code Assistant uses three structured error codes: `AI_TIMEOUT`, `AI_ERROR`, and `INTERNAL_ERROR`. Each returns a user-facing message that describes the problem without exposing internals. The `makeToolError` helper enforces this at the function level — every error path goes through one function that strips context before returning.

```typescript
function makeToolError(
  code: 'AI_TIMEOUT' | 'AI_ERROR' | 'INTERNAL_ERROR',
  userMessage: string
): CallToolResult {
  return {
    isError: true,
    content: [{ type: 'text', text: `[${code}] ${userMessage}` }],
  };
}
```

The AI timeout is a `Promise.race` between the inference call and a 30-second deadline. When the deadline wins, the response explains the timeout and suggests the user retry — it doesn't hang indefinitely or return a cryptic error.

## Decision 6: Testing Without Burning Money

MCP servers that call AI models are expensive to test. Every test run that hits real inference costs real money. The solution is mocking — but mocking AI calls well is harder than mocking a REST API because the response format varies by model and the envelope structure isn't always documented.

CF Code Assistant's test suite mocks the `env.AI.run()` binding at the Vitest level using `@cloudflare/vitest-pool-workers`. The mock returns realistic response structures for each tool, including the OpenAI-style response envelope that Cloudflare Workers AI uses for certain models (where the actual text is nested inside `response.choices[0].message.content` rather than `response.response`).

The test suite covers 108 cases across 8 files: auth flows, all 12 tool handlers, input validation boundaries, model routing with KV fallback, rate limiting, error sanitization, and structured logging output. Statement coverage is 95.5%. Total test execution time is under 5 seconds because nothing leaves the machine.

The envelope parsing discovery is worth calling out. During development, some models returned `{ response: "..." }` and others returned `{ choices: [{ message: { content: "..." } }] }`. The production code handles both, and the tests verify both paths. This kind of format inconsistency across models on the same platform is the sort of thing you only discover by building real tools against real inference endpoints.

## Decision 7: Observability From Day One

The server logs three categories of structured JSON events: `tool_invocation` (tool name, tier, model, latency in milliseconds), `tool_error` (error type, input size in bytes, no secrets), and `auth_event` (attempt type, success/failure, IP address for audit trail).

This isn't about debugging — it's about operating. When you're running an MCP server that your development workflow depends on, you need to answer questions like: which tools are actually getting used? What's the p50 latency on `generateCode`? Are there auth failures I should investigate? Structured logging answers these questions from `wrangler tail` without adding a third-party observability stack.

## The Single-File Question

CF Code Assistant is 760 lines in a single `src/index.ts`. This was a deliberate choice, not laziness. The server has one job (route MCP tool calls to Workers AI), one deployment target (a single Cloudflare Worker), and one operational concern (keep inference costs low while maintaining quality).

Splitting it into modules would add import/export ceremony, make the deployment artifact harder to audit, and create file-navigation overhead for a codebase that fits on a few screens. When the server grows beyond its current scope — if I add persistent session state or multi-model routing with fallback chains — it'll be time to split. Until then, one file keeps the cognitive overhead low.

## What I'd Build Differently

**Start with structured logging.** I added observability in Phase 4. It should have been Phase 1. Every debugging session before that was `console.log` archaeology in `wrangler tail`, which is slow and lossy. Structured logging from the start would have saved hours during the auth implementation alone.

**Test the auth flow first.** Auth is the most stateful, most complex part of the server. I tested tool handlers first because they were more interesting. The auth bugs I discovered later were more painful to fix because the test infrastructure wasn't designed around OAuth flows initially.

**Define the model allowlist as a type.** The model allowlist started as a runtime array check. It should have been a TypeScript literal type from the beginning, so the compiler catches invalid model names at build time rather than at runtime. A small thing, but representative of the general principle: push validation to the earliest possible moment.

## The Takeaway

Building an MCP server that works in a demo is trivial. Building one you'd trust in your daily workflow requires the same engineering discipline as any production service: authentication, input validation, error handling, testing, and observability. The MCP protocol itself is well-designed and the SDK is solid. The gap is in the guidance around everything that surrounds the protocol — the operational concerns that turn a prototype into infrastructure.

CF Code Assistant is open source at [github.com/russellkmoore/cf-code-assistant](https://github.com/russellkmoore/cf-code-assistant).
