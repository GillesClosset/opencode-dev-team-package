# WISC Framework Overview

## The Problem: Context Rot

Large language models have big context windows — but bigger isn't better.
Research from Chroma ("Longer Context, Worse Performance") shows that as
context grows, model performance degrades. Irrelevant information becomes
a distractor, causing the model to lose focus on the actual task.

In practice this means: the more files, docs, and history you dump into an
AI coding agent's context, the more likely it is to make mistakes, ignore
instructions, or produce inconsistent output. This is **context rot**.

## The Framework: WISC

WISC is a context engineering framework that solves context rot through
four pillars:

### Write

Externalize important information into persistent files instead of keeping
it only in the conversation. Plans, handoffs, enriched commits, and stories
survive across sessions. The git log becomes long-term memory.

### Isolate

Run research, exploration, and planning in separate agent contexts. When a
scout agent reads 50 files to answer a question, only the 500-token summary
enters the main agent's context — not the 50,000 tokens of raw file content.

### Select

Organize project knowledge into layers that load only when relevant.
Not everything needs to be in context all the time. On-demand rules load
per zone, reference docs load via scout assessment, and live exploration
happens through prime commands.

### Compress

Keep what you externalize lean and scannable. Handoff documents are under
100 lines. Commit messages capture WHY, not just what. Plans are structured
for machine consumption, not human prose.

## The 4-Layer Context System

### L1: Global Rules (AGENTS.md) — Always Loaded

The project's core identity: tech stack, structure, key commands, architecture
summary, universal conventions. Target: under 500 lines. Everything here is
loaded into every session, so keep it lean.

### L2: On-Demand Rules (`.opencode/rules/`) — Loaded Per Zone

Zone-specific conventions loaded only when working in that area. Examples:
`frontend.md`, `api.md`, `testing.md`. Target: 100-300 lines per file.

### L3: Reference Docs (`.opencode/docs/`) — Scout-Loaded

Deep-dive documentation with scout headers. A scout agent reads the 3-line
header to decide relevance before the main agent commits context to loading
the full document. Target: 200-500 lines per doc.

### L4: Prime Commands (`/prime`) — Live Exploration

Commands that explore the live codebase at session start: project structure,
recent git history, current branch state, existing handoffs. Routes through
a scout agent to keep the main context clean.

## The Daily Workflow

1. **Prime** — `/prime` to orient on the current project state
2. **Plan** — `/plan {feature}` to create a detailed plan plus a refined story
3. **Execute** — `/execute {story-file}` or `/execute {plan-file}` to implement the lot step by step
4. **Commit** — `/commit` to create an enriched commit with context
5. **Handoff** — `/handoff` to capture session state for continuation

**Key rule:** Never plan and implement in the same context. The planning
session's research noise should not pollute the implementation session.

## Key Principle

> Give the agent exactly what it needs, nothing more.

Every token in context should earn its place. If it's not directly relevant
to the current task, it's a distractor that degrades performance.

## References

- [Chroma Research: Longer Context, Worse Performance](https://research.trychroma.com/evaluating-chunking)
- [Anthropic: Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents)
- [WISC Framework — GitHub](https://github.com/coleam00/WISC)
- [WISC Framework — YouTube](https://www.youtube.com/watch?v=5MBPHxkIvFo)
