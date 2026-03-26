# Daily WISC Workflow

This is the practical guide to using the WISC commands in your daily
development workflow.

---

## Starting a Session

1. **Run `/prime`** (or a zone-specific variant like `/prime-backend`)
   - The scout agent analyzes the project structure
   - Reports recent git activity and current branch state
   - Checks for handoff files from previous sessions
   - Returns a concise summary to orient the main agent

2. **Check for handoffs**
   - If `/prime` reports an existing handoff, read it
   - Decide: continue the previous work, or start something new

3. **Load zone rules** (if needed)
   - If working in a specific area, load the relevant on-demand rule:
     "I'm working on the frontend — load `.opencode/rules/frontend.md`"

---

## Planning (Separate Context from Implementation)

1. **Describe the feature or fix** to the main agent
   - Be specific about what you want and why

2. **Main agent decides the approach:**
   - **Small, obvious fix** → skip to implementation
   - **Non-trivial work** → needs a plan

3. **Run `/plan {feature description}`**
   - Auto-routes to the discovery-architect agent
   - Discovery-architect uses scout sub-agents for research
   - Produces a structured plan in `.opencode/plans/`
   - Returns a summary with task count, complexity, and risks

4. **Review the plan**
   - Approve it, or request adjustments
   - The plan file is the contract for implementation

---

## Implementation (Fresh Context)

1. **Start a new session** (or clear the planning context)
   - The key WISC principle: implementation should not inherit
     the planning session's research noise

2. **Run `/execute .opencode/plans/{plan-name}.md`**
   - Auto-routes to Cody (delivery agent)
   - Cody reads the entire plan before writing any code
   - Verifies the working tree is clean
   - Implements tasks in dependency order
   - Validates incrementally after each task group
   - Runs the full validation suite
   - Returns a structured completion report

3. **Review the completion report**
   - Check validation results
   - Run any manual verification steps

---

## Committing

1. **Run `/commit`**
   - Reviews all changes (staged and unstaged)
   - Creates an enriched commit with:
     - Conventional commit tag (feat, fix, refactor, etc.)
     - WHY-focused body
     - `Context:` section if AI files were modified

2. **Why enriched commits matter**
   - The git log is long-term memory
   - Future `/prime` runs use `git log` to understand project history
   - Context changes captured in commits make the AI layer's evolution visible

---

## Ending a Session

1. **If work is complete:** commit and done
2. **If work will continue:** run `/handoff`
   - Captures: goal, completed work, next steps, key decisions, dead ends
   - Saves to `.opencode/handoffs/HANDOFF.md`
   - Next session: `/prime` detects and surfaces the handoff

---

## Key Rule

> **Never plan and implement in the same session context.**

The planning session reads dozens of files, explores the codebase, and
evaluates alternatives. That research is valuable for producing the plan,
but it's noise during implementation. The plan file captures the decision;
the implementation session only needs the plan.

This is the **Isolate** pillar in action.

---

## Quick Reference

| Action | Command | Routes to |
|--------|---------|-----------|
| Orient | `/prime` | Scout |
| Plan | `/plan {description}` | Discovery-architect |
| Implement | `/execute {plan-file}` | Cody |
| Commit | `/commit` | Current agent |
| Handoff | `/handoff` | Current agent |
