---
name: perplexity
description: Use this skill when you need to validate current best practices, compare libraries, research security advisories, or check whether a technology choice is still current — live web research via the Perplexity CLI.
---

# Perplexity — Live Web Research

## When to activate

Load this skill when:
- Validating an architectural decision against current best practices
- Comparing libraries or frameworks to determine which is preferred today
- Checking whether a pattern you know from training is still recommended
- Researching recent security advisories or known vulnerabilities
- Verifying that an API, service, or package is still active and maintained
- Answering "is this still the way people do X?" questions before recommending an approach

Do NOT use perplexity for library API specifics — use context7-mcp for that.
Perplexity answers "is this current?" and "what's the best approach?". Context7 answers "how does this API work?".

## Install

The Perplexity CLI tooling evolves — verify the current install method before use:

```
Check the current Perplexity CLI at: https://github.com/perplexity-ai/cli
or use the API directly with: npx perplexity-cli (verify package name before use)
Set PERPLEXITY_API_KEY in your environment.
```

If a stable global install is available:
```bash
npm install -g @perplexity-ai/cli   # verify current package name
```

## Authentication

```bash
export PERPLEXITY_API_KEY="your-api-key"
# or: perplexity auth   (if the CLI supports interactive auth)
```

Get your API key at: https://www.perplexity.ai/settings/api

## Workflow

1. **Formulate a focused question** — be specific and current-state oriented:
   - Good: "What is the recommended way to handle JWT refresh token rotation?"
   - Bad: "tell me about authentication"

2. **Run the CLI:**
   ```bash
   perplexity search "<your focused question>"
   ```

3. **Extract key findings** — identify the 2-3 most relevant points from the response.

4. **Include source in summary** — when reporting findings to the calling agent or user, cite where the information came from (URL or publication date if available).

## Guardrails

- Use perplexity to validate direction, not to copy implementation details verbatim.
- Perplexity responses may include outdated or incorrect information — treat them as a research starting point, not ground truth.
- For implementation details (method signatures, config options, API contracts), always use context7-mcp or the official documentation directly.
- If the question is about how a specific library's API works, use context7-mcp instead.

## Example queries

```bash
perplexity search "Is Prisma still the recommended ORM for Next.js?"
perplexity search "Current best practices for refresh token rotation with JWTs"
perplexity search "Is there a known security issue with bcrypt.js?"
perplexity search "tRPC vs REST vs GraphQL for a new TypeScript project"
```
