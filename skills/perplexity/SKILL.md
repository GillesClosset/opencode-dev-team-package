---
name: perplexity
description: Use this skill when you need to validate best practices, check current library versions, compare technologies, or verify that an architectural recommendation is still current — provides web research via the Perplexity MCP server.
---

# Perplexity — Web Research via MCP

## When to activate

Load this skill when:
- Recommending an architectural approach and you are not confident it is still current best practice
- Comparing libraries, frameworks, or tools where the landscape may have shifted since training data
- Checking whether a dependency version, API, or convention is still recommended
- Validating security advisories or vulnerability disclosures for a dependency
- Researching an unfamiliar technology, protocol, or service before scoping a lot

Do NOT use this skill for:
- Looking up specific library API signatures or method docs — use **Context7** instead
- Implementation details already covered by the plan — Perplexity is for research, not coding
- Questions answerable from the codebase itself — use scout or direct file reads

## Prerequisites

This skill requires the **official Perplexity MCP server** configured in
`opencode.json`. See the setup instructions below.

If the Perplexity MCP tools are not available in the current session, flag
this as a risk in the plan rather than guessing from training data.

## Available MCP tools

The Perplexity MCP server exposes four tools. Choose the right one for the task:

| Tool | Model | Use when |
|------|-------|----------|
| `perplexity_search` | Search API | Quick factual lookups: "what is the current stable version of X", "is library Y deprecated" |
| `perplexity_chat` | sonar-pro | Conversational queries: "what are the tradeoffs between approach A and B for this use case" |
| `perplexity_research` | sonar-deep-research | Deep analysis: "comprehensive comparison of auth strategies for a multi-tenant SaaS" |
| `perplexity_reason` | sonar-reasoning-pro | Analytical reasoning: "given these constraints, which architecture minimizes operational cost" |

### Tool selection guide

1. **Start with `perplexity_search`** for factual questions (versions, deprecation status, quick checks). It's fast and cheap.
2. **Use `perplexity_chat`** when you need a conversational answer with context — "how should I handle X given Y".
3. **Escalate to `perplexity_research`** only for deep-dive questions that justify the cost and latency — comprehensive comparisons, market analysis, multi-factor decisions.
4. **Use `perplexity_reason`** for structured analytical problems — cost modeling, decision matrices, complex tradeoff analysis.

## Query formulation

Good queries are specific and scoped. Bad queries are vague.

**Good:**
- "What is the current recommended way to handle refresh token rotation in OAuth 2.1 as of 2025?"
- "Is the `passport` npm package still actively maintained? What are the recommended alternatives?"
- "Compare Drizzle ORM vs Prisma for a TypeScript project using PostgreSQL — focus on migration workflow and type safety"

**Bad:**
- "best database" (too vague)
- "how to write a REST API" (too broad, not a research question)
- "FastAPI authentication" (use Context7 for library-specific docs)

## Workflow

1. **Identify the uncertainty** — what exactly are you not confident about?
2. **Formulate a precise query** — include the technology, version context, and what you need to know
3. **Choose the right tool** — `perplexity_search` for facts, `perplexity_chat` for nuance, `perplexity_research` for depth
4. **Extract actionable findings** — pull out the specific answer, version, or recommendation
5. **Cite in the plan** — note the finding and that it came from web research, so it can be verified

## Guardrails

- **Perplexity answers "is this current?" — Context7 answers "how does this API work?"** Do not confuse the two. Use Perplexity for ecosystem state and best practices; use Context7 for library documentation and method signatures.
- **Do not use Perplexity to generate code.** It returns research findings, not implementation.
- **Strip thinking tokens when context is tight.** Use `strip_thinking: true` for `perplexity_research` and `perplexity_reason` to save context tokens.
- **Flag unavailability as a risk.** If the MCP server is not configured, say so explicitly in the plan rather than falling back to training-data memory for version-sensitive claims.

## MCP server setup

Add the following to your OpenCode config (`opencode.json` or `~/.config/opencode/opencode.json`):

```json
"perplexity": {
  "type": "local",
  "command": ["npx", "-y", "@anthropic/perplexityai-modelcontextprotocol"],
  "environment": {
    "PERPLEXITY_API_KEY": "<your-perplexity-api-key>"
  },
  "enabled": true
}
```

Get an API key at [perplexity.ai](https://www.perplexity.ai/settings/api).
