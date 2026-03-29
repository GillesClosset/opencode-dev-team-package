# WISC for OpenCode

**Write · Isolate · Select · Compress** — A context engineering framework
for AI coding agents that prevents context rot and maintains reliability.

## What is WISC?

**Write** — Externalize knowledge into persistent files. Plans, handoffs,
enriched commits, and stories survive across sessions. Git log becomes
long-term memory.

**Isolate** — Run research in separate agent contexts. A scout reads 50
files and returns a 500-token summary — not 50,000 tokens of raw content.

**Select** — Organize knowledge into layers that load only when relevant.
Not everything needs to be in context all the time.

**Compress** — Keep externalized artifacts lean and scannable. Handoffs
under 100 lines. Commits capture WHY, not just what.

## What's in This Package?

```
wisc-opencode/
├── commands/           # 5 slash commands — the WISC engine
├── agents/             # 5 agents (1 orchestrator + 4 specialists)
├── skills/             # 7 agent skills (perplexity, frontend-ui, frontend-design, api-design, database, security, webapp-testing)
├── scaffold/           # Directory templates for .opencode/
├── examples/           # Example rules, docs, prime commands, AGENTS.md
├── docs/               # Framework documentation
└── templates/          # Story and artifact templates
```

## Quick Start

From your project root:

```bash
cp -r /path/to/wisc-opencode/commands/ .opencode/commands/
cp -r /path/to/wisc-opencode/agents/ .opencode/agents/
mkdir -p .opencode/{rules,docs,plans,handoffs}
mkdir -p .opencode/backlog/{refined,active,done,blocked}
cp /path/to/wisc-opencode/scaffold/rules/_template.md .opencode/rules/
cp /path/to/wisc-opencode/scaffold/docs/_template.md .opencode/docs/
```

Then write your `AGENTS.md` using `examples/agents-md-example.md` as a starting point.
See [docs/installation.md](docs/installation.md) for the full guide.

## Commands

| Command | Description | Routes to |
|---------|-------------|-----------|
| `/prime` | Orient on the project before starting work | Scout |
| `/plan {feature}` | Create a structured implementation plan | Discovery-architect |
| `/execute {plan}` | Implement a plan file step by step | Cody |
| `/commit` | Create an enriched commit with context | Current agent |
| `/handoff` | Capture session state for continuation | Current agent |

## Agents

| Agent | Role |
|-------|------|
| **Main** | Orchestrator — owns user conversation, drives WISC cycle, delegates to specialists |
| **Scout** | Lightweight research — explores codebase, returns concise summaries |
| **Cody** | Technical delivery — implements bounded lots with minimal safe changes |
| **Discovery-architect** | Scoping specialist — reduces uncertainty, defines executable lots |
| **QA-review** | Review specialist — assesses coherence, risk, and release readiness |

## The 4-Layer Context System

| Layer | Location | Loading | Budget |
|-------|----------|---------|--------|
| L1 | `AGENTS.md` | Always | <500 lines |
| L2 | `.opencode/rules/` | On-demand per zone | 100-300 lines |
| L3 | `.opencode/docs/` | Scout-assessed | 200-500 lines |
| L4 | `/prime` commands | Session start | <300 word output |

See [docs/context-layers.md](docs/context-layers.md) for details.

## Daily Workflow

1. **Prime** → `/prime` to orient on project state
2. **Plan** → `/plan {feature}` to create a plan plus a refined story
3. **Execute** → `/execute {story-file}` or `/execute {plan-file}` to implement (in fresh context)
4. **Commit** → `/commit` to create enriched commit with WHY
5. **Handoff** → `/handoff` to capture state for next session

**Key rule:** Never plan and implement in the same context.

See [docs/daily-workflow.md](docs/daily-workflow.md) for the full guide.

## Customization

- Create zone-specific prime commands (`/prime-backend`, `/prime-frontend`)
- Create on-demand rules for your project's work zones
- Create reference docs with scout headers for heavy documentation
- Customize existing commands with project-specific conventions

See [docs/customization.md](docs/customization.md) for details.

## Skills

Skills give agents specialized domain knowledge and live research capabilities.
Each skill is a `SKILL.md` file loaded on-demand when the task matches the skill's trigger condition.
This package currently ships **7 skills**.

| Skill | Description | Assigned to |
|-------|-------------|-------------|
| **perplexity** | Live web research — validate best practices and current ecosystem state via the Perplexity CLI | main, discovery-architect, scout, qa-review |
| **frontend-ui** | UI component patterns, chat/streaming interfaces, accessibility checklists | main, discovery-architect, cody |
| **frontend-design** | Frontend visual direction, typography, color/motion cohesion, anti-generic design guardrails | main, discovery-architect, cody |
| **api-design** | REST, GraphQL, and tRPC conventions, error formats, versioning | main, discovery-architect, cody |
| **database** | Schema design, indexing guidelines, migration best practices | main, discovery-architect, cody |
| **security** | OWASP Top 10 checklist, auth patterns, input validation | main, discovery-architect, cody, qa-review |
| **webapp-testing** | Playwright-based browser validation workflows for local web apps | main, discovery-architect, cody, qa-review |

Install skills into your project:
```bash
mkdir -p .agents/skills
cp -r /path/to/wisc-opencode/skills/security/ .agents/skills/
cp -r /path/to/wisc-opencode/skills/database/ .agents/skills/
# etc. — install only what your project needs
```

See [docs/installation.md](docs/installation.md#install-skills) for full install instructions and Perplexity CLI setup.
See [docs/customization.md](docs/customization.md#creating-new-skills) for how to create your own skills.

## Documentation

- [WISC Overview](docs/wisc-overview.md) — The framework explained
- [Installation](docs/installation.md) — Step-by-step setup
- [Context Layers](docs/context-layers.md) — The 4-layer system in detail
- [Daily Workflow](docs/daily-workflow.md) — Practical usage guide
- [Onboarding](docs/onboarding-existing-project.md) — Adding WISC to existing projects
- [Customization](docs/customization.md) — Extending for your project

## Credits

- [WISC Framework](https://github.com/coleam00/WISC) by Cole Medin
- [Context Engineering for AI Coding Agents](https://www.youtube.com/watch?v=5MBPHxkIvFo) — YouTube
- [Chroma Research: Evaluating Chunking](https://research.trychroma.com/evaluating-chunking) — Context length research
- [Anthropic: Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents)
