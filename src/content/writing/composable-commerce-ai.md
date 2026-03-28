---
title: "Composable Commerce in the AI Era"
description: "How twenty years of e-commerce platform evolution led to a moment where AI agents are the next composable interface — and what that means for platform builders."
pubDate: 2026-02-10
tags: ["AI", "Commerce", "Architecture", "MCP"]
draft: false
---

## I've Seen This Movie Before

I've been building e-commerce platforms for over twenty years, starting at AT&T when we were turning a marketing website into a transactional system that would eventually process $2B+ in annual revenue. I co-founded Spark::red to provide managed hosting for Oracle Commerce (ATG) implementations serving brands like Burberry, Restoration Hardware, and Michael Kors. I led the $30M combined division at Pivotree through IPO preparation.

In that time, I've watched the industry go through three major architectural shifts: monolithic platforms to service-oriented architecture, SOA to microservices, and microservices to composable/MACH. Each transition followed the same pattern — the old approach hits a complexity ceiling, a new abstraction layer emerges, early adopters prove it out, and the ecosystem gradually follows.

We're in the early stages of a fourth transition, and this one is different. The composable interface isn't a new API pattern or deployment model. It's AI agents.

## The Composable Promise, Delivered Unevenly

The MACH Alliance (Microservices, API-first, Cloud-native, Headless) articulated something that practitioners already knew: the monolithic commerce platform was a constraint, not a foundation. When your product catalog, pricing engine, checkout flow, and content management are welded together in a single vendor's architecture, every business decision becomes a platform decision.

Composable commerce was supposed to fix this. Pick best-of-breed services, connect them via APIs, swap components without rebuilding the whole system. And it did fix it — for the first set of interfaces. A headless commerce backend can serve a website, a mobile app, and a kiosk through different frontends sharing the same product and order APIs.

But composable commerce in practice still assumes that the interface is human-operated. Someone browses a catalog, adds items to a cart, enters shipping information, and completes checkout. The APIs exist to serve those human workflows through different channels.

What happens when the interface is an AI agent?

## The Agent Interface

I built a 17-tool MCP server on top of Mercora — my edge-native e-commerce platform — specifically to explore this question. The server exposes the full commerce workflow as tools that any MCP-compatible AI client can consume: product search with semantic vector matching, cart management with persistent sessions, order placement with shipping and payment validation, and fulfillment assessment that tells the agent what the store can and can't provide.

The interesting part isn't the individual tools. It's how the interaction model changes.

When a human shops, they navigate a catalog, compare products, and make sequential decisions through a visual interface. When an AI agent shops, it starts with an intent ("outfit my team of 15 for a Colorado camping trip in September"), decomposes it into requirements, assesses fulfillment capability across potentially multiple retailers, assembles a cart, and presents the result for approval. The agent doesn't browse — it reasons about the request and executes against available capabilities.

This is composable commerce at a higher level of abstraction. The commerce platform isn't just headless — it's agentless. It exposes capabilities rather than pages, and the consuming interface decides how to orchestrate them.

## What Changes

**Discovery becomes semantic, not navigational.** In a traditional storefront, discovery is constrained by the category taxonomy and search index the retailer built. An AI agent can describe what it needs in natural language ("ultralight shelter under 2 pounds for three-season use") and the semantic search layer matches intent to products by meaning rather than keywords. The retailer's category hierarchy becomes an internal implementation detail rather than the primary discovery mechanism.

**Multi-site coordination becomes possible.** The `assess_request` tool in Mercora's MCP server returns not just what the store can fulfill, but what it can't — explicitly flagging items that need to be sourced elsewhere. This enables orchestrating agents to coordinate purchases across specialized retailers. Your camping gear comes from Mercora, your freeze-dried meals from another retailer, your permit application from a government API. One agent, multiple fulfillment sources, unified for the user.

**The API contract shifts from CRUD to capability.** Traditional commerce APIs are structured around resources: products, carts, orders. Agent-facing APIs need to be structured around capabilities: "What can you do?", "How well can you fulfill this request?", "What are my options?" The `assess_request` tool pattern — where the store evaluates a natural language request and returns a fulfillment score — is fundamentally different from a product search endpoint.

**Authentication changes shape.** When the shopper is an AI agent, traditional user authentication doesn't make sense. Mercora's MCP server uses API key-based agent authentication with per-agent rate limiting, spending controls, and permission levels. The agent carries user context (budget, preferences, location) as metadata rather than requiring the user to authenticate directly. The trust relationship is between the orchestrating system and the retailer, not between the end user and the retailer.

## What Doesn't Change

The fundamentals of commerce don't change just because the interface does. Inventory is still inventory. Pricing rules are still pricing rules. Tax calculation, payment processing, fulfillment logistics — all of this remains. The MACH-compliant data model that serves Mercora's web storefront is the same data model that serves the MCP tools. A product is a product regardless of whether a human or an agent is buying it.

This is the payoff of composable architecture. If your commerce backend is properly headless, adding an agent interface is additive — you're building a new presentation layer, not rebuilding the platform. The work I did at Spark::red and Pivotree building service-first commerce infrastructure for Fortune 500 brands was solving the same fundamental problem: separate the commerce logic from the interface so you can change the interface without breaking the business.

The agent interface is just the latest — and potentially most transformative — instance of that principle.

## Where This Goes

I'm not claiming that AI agents will replace web storefronts tomorrow. People will continue to browse, compare, and enjoy the experience of shopping through visual interfaces for a long time.

But for certain purchase categories — routine replenishment, corporate procurement, trip outfitting, gift buying with specific constraints — an agent interface is strictly better than a human-operated one. The agent can hold more context, evaluate more options, coordinate across more sources, and execute faster than a human clicking through web pages.

The retailers who will benefit most from this transition are the ones who already have composable backends and can expose their capabilities as tool interfaces without rearchitecting. The ones who are locked into monolithic platforms will face the same migration pain they faced during every previous architectural shift — except this time, the competitors who move first won't just have a better website. They'll have an entirely new sales channel that operates at machine speed.

For platform builders, the message is straightforward: if your commerce backend can't serve an AI agent as cleanly as it serves a web browser, your composable architecture isn't as composable as you think.
