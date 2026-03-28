---
title: "The Real Cost of LLM Tool Calling"
description: "What I learned building guardrails around tool-calling models that narrate instead of act, log data on hypothetical questions, and get stuck in infinite loops."
pubDate: 2026-03-19
tags: ["AI", "Architecture", "Cloudflare Workers"]
draft: false
---

## The Promise vs. The Reality

Tool calling is the feature that makes LLMs useful for real applications. Instead of just generating text, the model can call functions — log a meal, update a database, send an email. Every model provider markets this as a solved problem. Define your tools, pass them to the model, and it calls the right one with the right arguments.

In practice, it's nowhere near that clean. I've spent months building RecompAI, an AI-powered body recomposition coaching platform running on Cloudflare Workers AI. The app has 20+ tools across meal logging, supplement protocol management, workout tracking, and body metrics. Users interact through natural conversation, and the AI is expected to both respond conversationally and execute structured operations.

Here's what the marketing materials don't tell you: models are unreliable at multi-step tool calling. They narrate actions instead of executing them. They log data on hypothetical questions. They get stuck in infinite loops calling the same tool repeatedly. They skip required steps because they "know" the answer from training data. Every one of these failure modes required a code-level guardrail — not a prompt tweak, not a temperature adjustment, but actual engineering around the model's limitations.

## Problem 1: Models That Narrate Instead of Act

The most common failure mode is also the most frustrating. You tell the model "I had chicken and rice for lunch." The model responds: "I'll log that meal for you with the following macros..." — but no `log_meal` tool call appears. The model described what it would do instead of doing it.

This happens more often than you'd expect, even with `temperature: 0`. The model treats tool calling as optional commentary rather than a required action. My first attempt at fixing this was prompt engineering — adding instructions like "You MUST call tools, do not describe what you would do." That helped maybe 60% of the time.

The real fix is a nudge-retry mechanism in the tool loop itself:

```typescript
if (round === 0 && nudgeToolUse && tools.length > 0 && !nudged) {
  const responseText = result?.response ?? '';
  msgs.push({ role: 'assistant', content: responseText });
  msgs.push({
    role: 'user',
    content: 'Please use the available tools to complete this action. '
           + 'Do not describe what you would do — call the tools directly.',
  });
  nudged = true;
  continue;
}
```

When the model returns text without tool calls on an action-phrased message, the system appends a nudge and retries once. This single retry catches the majority of narration failures. The key insight: you only nudge once. Multiple retries create worse problems than the original failure.

## Problem 2: Writing Data on Hypothetical Questions

Users ask questions like "What would happen if I ate 200g of protein?" or "How much protein is in a chicken breast?" These are questions, not logging requests. But give a model access to a `log_meal` tool and it will eagerly log data for hypothetical scenarios.

Prompt instructions are insufficient here. You can tell the model "do not log data for hypothetical questions" and it will still occasionally decide the user "probably wants" the data logged. The model is trying to be helpful in a way that corrupts your data.

The solution that actually works is removing the possibility entirely. A regex-based detector identifies hypothetical and question intent, and when triggered, the system strips write tools from the schema before the AI call:

```typescript
const HYPOTHETICAL_PATTERN = /\b(what\s+would|what\s+should|if\s+i|
  would\s+it|should\s+i|what\s+if|how\s+(much|many|often))\b/i;

if (isHypotheticalMessage(message)) {
  filteredTools = domainTools.filter(
    tool => !WRITE_TOOLS.has(tool.function.name)
  );
}
```

The model literally cannot call `log_meal` because it doesn't exist in its schema for that request. This is more reliable than any prompt instruction because it removes the capability rather than asking the model to exercise restraint.

## Problem 3: Skipping Required Steps

RecompAI uses a USDA food database for accurate nutritional data. The correct workflow is: search the USDA database, find the matching food, then log the meal with verified macro values. But models will skip the search step and estimate macros from training data — especially for common foods where the model "knows" that chicken breast has roughly 31g of protein per 100g.

The problem is that "roughly" isn't good enough for a nutrition tracking app. Users trust the numbers. If the model estimates instead of looking up, the accumulated error over days and weeks makes the tracking meaningless.

The guardrail is a code-level gate that blocks `log_meal` from executing unless `search_usda` has been called first in the same request:

```typescript
if (tc.name === 'log_meal' && !executedTools.has('search_usda')) {
  blocked.push({
    name: tc.name,
    reason: 'You must call search_usda for each food item BEFORE '
          + 'calling log_meal. Search USDA first, then log with the '
          + 'nutritional data from the search results.',
  });
  continue;
}
```

The blocked tool call returns a synthetic error to the model explaining what it needs to do first. The model then self-corrects on the next round, calls `search_usda`, gets real data, and logs with accurate values. This pattern — block and explain rather than block and fail — lets the model recover within the same request.

## Problem 4: Infinite Loops

Models can get stuck calling the same tool with the same arguments repeatedly. This usually happens when the tool returns a result the model doesn't know how to interpret, so it tries again hoping for a different outcome. Without detection, this burns through your token budget and your users' patience.

The fix is straightforward: track call signatures across rounds and break the loop when a duplicate is detected.

```typescript
const callSignature = JSON.stringify(
  normalizedCalls.map(tc => ({ n: tc.name, a: tc.arguments }))
);
if (previousCallSignatures.has(callSignature)) {
  console.warn('duplicate tool call detected, breaking loop');
  break;
}
previousCallSignatures.add(callSignature);
```

When a loop is detected, the system breaks out and generates a final response without tools. The user gets an honest "your request was processed" message rather than a timeout or an empty response.

## Problem 5: The Extra Call Problem

Cloudflare provides a `runWithTools` utility that's supposed to handle the tool-calling loop for you. It has a critical flaw: when the model returns a text response after processing tool results, `runWithTools` discards that response and makes another `ai.run` call for a "final response."

This second call is the problem. The model has already seen the tool results and composed a good response. The extra call lacks the context of what just happened and often produces worse, contradictory, or generic output. It also doubles your inference cost for every tool-calling interaction.

RecompAI replaces `runWithTools` with a custom tool loop that returns the model's response as soon as it stops calling tools, without a redundant extra call. This alone improved response quality noticeably and cut token usage on tool-calling requests roughly in half.

## The Bigger Picture

Every one of these guardrails exists because of the same root cause: LLMs are text generators that have been adapted for structured operations. They're good at understanding intent and generating plausible-looking tool calls. They're unreliable at executing multi-step workflows with the consistency that production applications require.

The current approach — adding guardrails around unreliable tool calling — works, but it's a lot of machinery compensating for something models aren't naturally great at. Cloudflare's recently announced Dynamic Workers point in a more interesting direction: instead of having the model step through tool calls one at a time, you give it a TypeScript API and let it write a function that does everything in one shot. One inference call generates code, one Worker executes it. That addresses the root cause rather than the symptoms.

I haven't made the switch yet. But as someone who's spent months engineering around these limitations, I'm watching closely.
