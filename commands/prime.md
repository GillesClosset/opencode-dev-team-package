---
description: Prime agent with project understanding before starting work
agent: scout
---

# Prime: Load Project Context

## Objective

Build comprehensive understanding of the project before beginning any work. This orients
you on structure, active state, and key architectural conventions.

## Process

### 1. Analyze Project Structure

List the top-level project structure. Identify whether this is a monorepo, single
package, or multi-service project. Note the build system and package manager.

```bash
ls -la
```

If a `package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, or similar exists,
read the first 30-40 lines to understand the project type and scripts.

### 2. Read Core Documentation

Read `AGENTS.md` in full — it contains the authoritative project conventions,
architecture reference, and development guidelines.

Check for on-demand rules:
```bash
ls .opencode/rules/ 2>/dev/null
```

### 3. Identify Key Entry Points

Identify the project's main entry points. Look for: main application file,
router/handler setup, configuration loading, key domain modules. Read the
first 50-80 lines of each to understand the flow.

### 4. Understand Dependencies

Understand the project's module/package structure and dependency direction.
Note which modules are foundational (few deps) vs. high-level (many deps).

### 5. Understand Current State

Check recent commits:
```bash
git log -10 --oneline
```

Check current branch and working tree status:
```bash
git status
```

Check for an existing handoff from a previous session:
```bash
cat .opencode/handoffs/HANDOFF.md 2>/dev/null
```

If a handoff file exists, include its contents in the report.

## Output Report

Provide a concise summary (under 300 words) covering:

### Project Overview
- Project name and purpose (1-2 lines)
- Tech stack and build system
- Project structure (monorepo, single package, etc.)

### Architecture
- Module/package structure and dependency direction
- Key interfaces or contracts
- Primary data and control flow

### Current State
- Active branch, recent changes, any uncommitted work
- Handoff from previous session (if exists)
- Any observations relevant to next task

**Keep it scannable — bullets over prose.**
