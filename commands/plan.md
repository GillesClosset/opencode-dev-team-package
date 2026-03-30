---
description: Create a structured implementation plan for a feature or fix
agent: discovery-architect
---

# Plan Feature: Structured Implementation Planning

## Objective

Produce a detailed, actionable implementation plan for: **$ARGUMENTS**

The plan will be saved to `.opencode/plans/{kebab-case-name}.md` and is designed to be
consumed by the `/execute` command.

---

## Phase 1: Feature Understanding

Restate the feature request in your own words. Identify:

1. **Problem being solved** — What user pain point or capability gap does this address?
2. **Success criteria** — What does "done" look like? How will we verify it works?
3. **Scope boundaries** — What is explicitly in scope vs. out of scope?
4. **Areas of impact** — Which parts of the codebase are affected?
5. **Interface changes** — Does this touch existing APIs, data models, or contracts?

---

## Phase 2: Codebase Intelligence

Use sub-agents to perform targeted codebase research in parallel. Spawn separate
sub-agents (the scout agent) for:

**Scout A — Affected area deep-dive:**
Read relevant source files in the affected area. Map the current data flow.
Identify every file that will need to change.

**Scout B — Interface and type contracts:**
Read existing API contracts, type definitions, and module exports. Understand
what interfaces exist and how they're consumed across the project.

**Scout C — Test patterns:**
Find existing test files similar to the area of change. Read 2-3 representative
test files to understand mocking patterns, assertion style, and test isolation
requirements.

**Scout D — Related prior work:**
```bash
git log --oneline -20
```
Read recent commits touching relevant files to understand change patterns.

Synthesize findings: current state, gaps, constraints.

---

## Phase 3: External Research (if needed)

If the feature involves external APIs, new libraries, or unfamiliar patterns,
use documentation lookup skills or web research to investigate:

- Relevant SDK or library documentation
- Known gotchas or version incompatibilities
- Community patterns for the problem domain

Document any specific findings that affect the implementation approach.

---

## Phase 4: Strategic Thinking

Before writing tasks, reason through:

**Architecture decisions:**
- Where does this logic belong? Apply SRP — keep each module focused on one concern.
- Does this require a new module, or extends an existing one?
- What's the dependency direction? Never create circular dependencies.

**Interface design:**
- Prefer extending existing narrow interfaces over creating fat ones.
- New interface methods only if they have a concrete current caller.

**Test isolation strategy:**
- Plan where new tests belong to avoid polluting existing test suites.
- Identify mocking requirements early.

**Rollback plan:**
- What is the blast radius if this goes wrong?
- Are changes reversible?

**Project conventions:**
- Flag any project-specific anti-patterns from AGENTS.md when one exists.
- If no root `AGENTS.md` exists, note the repo-local validation or workflow
  conventions that will govern execution instead of inventing AGENTS-only rules.

---

## Phase 5: Artifact Generation

Generate the implementation plan at `.opencode/plans/{kebab-case-feature-name}.md`:

```markdown
# Plan: {Feature Name}

## Overview
{1-2 sentence summary of what this implements and why.}

## Success Criteria
- [ ] {Verifiable criterion 1}
- [ ] {Verifiable criterion 2}
- [ ] Passes project validation suite

## Affected Areas
- {area} — {what changes}

## Architecture Notes
{Key decisions, tradeoffs, interface changes.}

## Status
`draft` | `refined` | `active` | `done` | `blocked`

## Lot Boundary
**In scope:**
- ...
**Out of scope:**
- ...

## Risks and Unknowns
- ...

## Implementation Tasks

### Task 1: {descriptive name}
**File:** {path}
**Type:** Create | Modify | Delete
**Description:** {What this task does and why.}
**Depends on:** {Task N, or "none"}

### Task 2: ...

## Validation Steps
1. {Run the project's validation commands as specified in AGENTS.md}
2. Manual test: {specific steps to verify the feature}

## Rollback Notes
{How to safely revert if needed.}
```

Set the plan status to `refined` when the lot is ready for implementation —
the acceptance criteria are testable and the lot boundary is explicit enough for
Cody to act on without redoing discovery.

### Task Ordering Rules
- Order by dependency (blocked tasks come after their dependencies).
- Group by area when possible to minimize context switching.
- Database/schema changes (if any) come first.
- Type/interface definitions before implementations.
- Tests after implementations.
- Frontend after backend API is stable.

---

## Output

1. Save the plan file to `.opencode/plans/{kebab-case-name}.md`
2. Print the plan to the conversation
3. Summarize: number of tasks, affected areas, estimated complexity (low/medium/high),
   and any risks or open questions that need resolution before execution.
