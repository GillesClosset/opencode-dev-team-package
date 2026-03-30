# Installation

## Prerequisites

- [OpenCode](https://opencode.ai) installed and working
- A project with a git repository

## Quick Install

From your project root:

```bash
# Copy commands (the WISC engine)
cp -r /path/to/wisc-opencode/commands/ .opencode/commands/

# Copy agents
cp -r /path/to/wisc-opencode/agents/ .opencode/agents/

# Create the context layer directories
mkdir -p .opencode/{rules,docs,plans,handoffs}
mkdir -p .opencode/backlog/{refined,active,done,blocked}

# Copy templates
cp /path/to/wisc-opencode/scaffold/rules/_template.md .opencode/rules/
cp /path/to/wisc-opencode/scaffold/docs/_template.md .opencode/docs/
```

Replace `/path/to/wisc-opencode/` with the actual path to your copy of this package.

## Install Skills

Skills give agents access to specialized domain knowledge and live research capabilities.
They are loaded on-demand — only when the task matches the skill's trigger condition.

### Project-local install (recommended)

Install skills into the current project only:

```bash
mkdir -p .agents/skills

# Install all 6 skills
cp -r /path/to/wisc-opencode/skills/frontend-ui/ .agents/skills/
cp -r /path/to/wisc-opencode/skills/frontend-design/ .agents/skills/
cp -r /path/to/wisc-opencode/skills/api-design/ .agents/skills/
cp -r /path/to/wisc-opencode/skills/database/ .agents/skills/
cp -r /path/to/wisc-opencode/skills/security/ .agents/skills/
cp -r /path/to/wisc-opencode/skills/webapp-testing/ .agents/skills/

# Or install only the skills your project needs
cp -r /path/to/wisc-opencode/skills/security/ .agents/skills/
cp -r /path/to/wisc-opencode/skills/database/ .agents/skills/
```

### Global install

Install skills for all projects on this machine:

```bash
mkdir -p ~/.agents/skills

cp -r /path/to/wisc-opencode/skills/frontend-ui/ ~/.agents/skills/
cp -r /path/to/wisc-opencode/skills/frontend-design/ ~/.agents/skills/
cp -r /path/to/wisc-opencode/skills/api-design/ ~/.agents/skills/
cp -r /path/to/wisc-opencode/skills/database/ ~/.agents/skills/
cp -r /path/to/wisc-opencode/skills/security/ ~/.agents/skills/
cp -r /path/to/wisc-opencode/skills/webapp-testing/ ~/.agents/skills/
```

All 6 skills provide reference checklists and conventions loaded directly into agent context. `webapp-testing` assumes the target project already has a local Playwright/browser setup when you want to run browser checks, but the skill itself ships only as guidance.

## Write Your AGENTS.md

Use `examples/agents-md-example.md` as a starting point. Copy it to your project
root as `AGENTS.md` and fill in the project-specific details.

**Key guidelines:**
- Keep it under 500 lines — this is always loaded (L1)
- Include: project overview, tech stack, structure, key commands, architecture, conventions
- Point to `.opencode/rules/` for zone-specific details
- List high-risk zones that need explicit authorization to modify

## Create Your First On-Demand Rule

Copy an example from `examples/rules/` and adapt it for your project's most
common work zone:

```bash
cp /path/to/wisc-opencode/examples/rules/frontend.md .opencode/rules/frontend.md
# Edit the file to match your project's frontend conventions
```

## Create Your First Reference Doc

If you have architecture documentation, format it with the scout header:

```markdown
# Architecture Deep Dive

> **Purpose:** End-to-end flow traces across the system.
> **When to use:** Understanding data flows, debugging cross-system issues.
> **Size:** ~300 lines — use the scout agent to check relevance before loading.

---

{Your architecture documentation here}
```

See `scaffold/docs/_template.md` for the template.

## Verify the Installation

1. Open your project in OpenCode
2. Type `/` — you should see: commit, plan, execute, handoff, prime
3. Run `/prime` — it should analyze your project and return a summary
4. The scout, cody, discovery-architect, and qa-review agents should appear
5. If you installed skills, verify them:
   ```bash
   ls .agents/skills/
   # Should list the skills you installed (e.g., frontend-design, webapp-testing, security)
   ```

## Optional: Create Zone-Specific Prime Commands

Copy an example from `examples/prime-commands/` into `.opencode/commands/`
and adapt for your project:

```bash
cp /path/to/wisc-opencode/examples/prime-commands/prime-backend.md .opencode/commands/
# Edit to match your project's backend structure
```

## Optional: Install Globally

If you want commands and agents available across all projects:

```bash
cp -r /path/to/wisc-opencode/commands/ ~/.config/opencode/commands/
cp -r /path/to/wisc-opencode/agents/ ~/.config/opencode/agent/
```

Global installation means the commands work everywhere, but zone-specific
rules and reference docs still live in each project's `.opencode/` directory.

## Optional: External Documentation (Context7)

Context7 gives agents access to **current, version-accurate library and API
documentation** at query time. Without it, agents fall back on training data
which is often months or years out of date — leading to plans with stale
package versions, deprecated APIs, or wrong call signatures.

### MCP server (recommended for OpenCode)

Add to `~/.config/opencode/opencode.json` under the `"mcp"` key:

```json
"context7": {
  "type": "remote",
  "url": "https://mcp.context7.com/mcp",
  "headers": {
    "CONTEXT7_API_KEY": "<your-api-key>"
  },
  "enabled": true
}
```

Get a free API key at [context7.com](https://context7.com). With the MCP
server configured, agents can resolve and query library documentation
directly through the `context7_resolve-library-id` and
`context7_query-docs` MCP tools — no extra commands needed.

### ctx7 CLI (alternative / bash fallback)

Install the CLI globally:

```bash
npm install -g context7-cli
```

Usage inside agent bash steps:

```bash
ctx7 get fastapi
ctx7 get "langchain python"
ctx7 get "pydantic v2"
```

The CLI is useful when an agent needs to resolve package versions in a bash
step (e.g., inside `/execute` or a scout research pass) without relying on
MCP tool availability.

### When to use each

| Situation | Use |
|-----------|-----|
| OpenCode session with MCP configured | Context7 MCP tools (automatic) |
| Agent writing a `requirements.txt`, `package.json`, or `pyproject.toml` | **Always** look up current version first |
| Scout researching an unfamiliar library | `ctx7 get <library>` in a bash step |
| Discovery-architect Phase 3 external research | Context7 MCP or `ctx7 get` |
| Any version-sensitive API or SDK usage | Look up before writing, not after |
