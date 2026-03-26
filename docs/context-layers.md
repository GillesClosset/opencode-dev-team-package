# The 4-Layer Context System

The WISC framework organizes project knowledge into four layers, each with
a different loading strategy and token budget. This prevents context rot
by ensuring only relevant information enters the agent's context.

---

## L1: Global Rules (AGENTS.md)

**Loading:** Always loaded at session start. Every session sees this.
**Target:** Under 500 lines.
**Location:** Project root `AGENTS.md`

### What goes here

- Project name and 1-2 sentence purpose
- Tech stack (language, framework, database, build tool)
- Project structure (directory tree, 10-15 lines)
- Key commands (test, lint, build, format, validate)
- Architecture summary (5-10 lines, high-level only)
- Universal conventions (import style, error handling, commit format)
- High-risk zones (areas requiring explicit authorization)
- Pointer to on-demand rules: "Zone rules are in `.opencode/rules/`"

### When to split to L2

If you're adding content specific to one area of the codebase (e.g.,
"Frontend components should use..." or "Database queries must..."),
it belongs in an on-demand rule file, not in AGENTS.md.

### Example

See `examples/agents-md-example.md` for a complete example.

---

## L2: On-Demand Rules (`.opencode/rules/`)

**Loading:** Loaded explicitly when work enters a specific zone.
**Target:** 100-300 lines per rule file.
**Location:** `.opencode/rules/{zone}.md`

### What goes here

- Zone-specific conventions, patterns, and anti-patterns
- Framework version details and critical differences
- Key files in the zone and their purposes
- Zone-specific testing commands and patterns
- Common mistakes to avoid in this area

### How it loads

The agent or user identifies the work zone at session start. The relevant
rule file is loaded explicitly: "I'm working on the frontend" triggers
loading `.opencode/rules/frontend.md`.

### When to split to L3

If a rule file references documentation that's over 200 lines (e.g., a
detailed API reference or flow trace), move that documentation to L3
and reference it from the rule file.

### Example

See `examples/rules/` for four complete examples (frontend, api, database, testing).

---

## L3: Reference Docs (`.opencode/docs/`)

**Loading:** Scout agent reads the header, decides relevance, recommends loading.
**Target:** 200-500 lines per document.
**Location:** `.opencode/docs/{topic}.md`

### What goes here

- Architecture deep dives with file references
- End-to-end flow traces
- Implementation guides for complex subsystems
- API references and integration documentation
- Decision records and design rationale

### The scout header

Every L3 document must include a 3-line header block:

```markdown
> **Purpose:** {1 sentence — what this document covers}
> **When to use:** {1 sentence — what kind of work makes this doc relevant}
> **Size:** ~{N} lines — use the scout agent to check relevance before loading.
```

The scout agent reads only this header to decide whether the main agent
should load the full document. This prevents speculative loading of large
documents that may not be relevant.

### When content is too large

If a document exceeds 500 lines, split it into focused sub-documents.
Each sub-document gets its own scout header.

### Example

See `examples/docs/architecture-deep-dive.md` for a complete example.

---

## L4: Prime Commands (Live Exploration)

**Loading:** Executed at session start via `/prime` or zone-specific variants.
**Target:** Produces under 300 words of summary.
**Location:** `.opencode/commands/prime.md` (and optional `prime-{zone}.md`)

### What it does

- Explores the live codebase: project structure, file listing
- Reads recent git history for project context
- Checks for handoff files from previous sessions
- Reads AGENTS.md and notes available on-demand rules
- Returns a concise summary to the main agent

### Why it routes through scout

The prime command routes to the scout agent so that the exploration
(reading dozens of files, listing directories, parsing git log) happens
in an isolated context. Only the structured summary enters the main
agent's context.

### Creating zone-specific primers

For projects with distinct zones (backend, frontend, data pipeline),
create zone-specific prime commands:

```bash
cp examples/prime-commands/prime-backend.md .opencode/commands/prime-backend.md
# Edit to match your project's backend structure
```

### Example

See `examples/prime-commands/` for backend and frontend primer examples.

---

## Summary

| Layer | Location | Loading | Budget | Split Signal |
|-------|----------|---------|--------|-------------|
| L1 | `AGENTS.md` | Always | <500 lines | Zone-specific content → L2 |
| L2 | `.opencode/rules/` | On-demand | 100-300 lines | Heavy docs → L3 |
| L3 | `.opencode/docs/` | Scout-assessed | 200-500 lines | Over 500 lines → split |
| L4 | `/prime` commands | Session start | <300 word output | N/A |
