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
cp -r /path/to/wisc-opencode/agents/ ~/.config/opencode/agents/
```

Global installation means the commands work everywhere, but zone-specific
rules and reference docs still live in each project's `.opencode/` directory.
