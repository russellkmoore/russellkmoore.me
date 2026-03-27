---
title: "MCP as an Integration Pattern: What I Learned Building Mercora"
description: "Practical lessons from implementing Model Context Protocol in a production e-commerce platform."
pubDate: 2026-03-26
tags: ["AI", "Architecture", "MCP"]
draft: false
---

## Why MCP Changes the Integration Game

When I started building Mercora, the initial approach was predictable: REST APIs, webhook callbacks, and a growing tangle of adapters connecting AI capabilities to e-commerce data. It worked, but every new integration meant another custom connector, another set of error handling patterns, and another surface area for things to break.

Model Context Protocol changed that equation. Instead of building point-to-point integrations between AI models and data sources, MCP provides a standardized interface -- a universal adapter pattern that lets any AI model talk to any tool or data source through a consistent protocol.

> The best integration patterns are the ones that make the next integration trivial, not just the current one possible.

## The Architecture in Practice

The core insight is that MCP servers act as capability providers. Each server exposes tools and resources through a well-defined schema, and AI models consume them without needing to know the implementation details. Here is a simplified example of how we structured a product catalog server:

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";

const server = new McpServer({
  name: "product-catalog",
  version: "1.0.0",
});

server.tool(
  "search_products",
  "Search the product catalog by query, category, or price range",
  {
    query: z.string().describe("Search terms"),
    category: z.string().optional(),
    maxPrice: z.number().optional(),
  },
  async ({ query, category, maxPrice }) => {
    const results = await catalog.search({ query, category, maxPrice });
    return {
      content: [{ type: "text", text: JSON.stringify(results, null, 2) }],
    };
  }
);
```

This single server definition replaces what used to be a REST controller, request validation middleware, and response serialization layer -- all in about 20 lines.

## Three Lessons From Production

After running MCP-powered integrations in production for several months, three patterns became clear:

- **Start with tools, not resources.** Tools give the AI model agency to act on data. Resources are useful for context, but tools are where the real value lives. We saw a 3x improvement in task completion rates when we converted read-only resources into actionable tools.

- **Schema design is your API contract.** Zod schemas in MCP tool definitions serve double duty: they validate inputs at runtime and they communicate capabilities to the AI model. Investing time in descriptive schemas pays off in fewer misunderstood requests.

- **Error boundaries matter more than you think.** When an AI model calls a tool and it fails silently, the model hallucinates a result. Explicit error responses with clear messages let the model recover gracefully or ask the user for help.

The net result: our integration development time dropped from weeks to days, and the AI-driven features became significantly more reliable. MCP is not just a protocol -- it is an architectural pattern that changes how you think about connecting systems.
