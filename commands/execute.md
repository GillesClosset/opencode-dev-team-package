---
description: Execute a plan or refined story step by step
agent: cody
---

# Execute: Implement a Plan or Refined Story

## Objective

Read and execute every task described by: **$ARGUMENTS**

`$ARGUMENTS` may be either:
- a plan file in `.opencode/plans/`, or
- a refined story in `.opencode/backlog/refined/` that references its companion plan.

Implement all tasks faithfully, following project conventions, and report results.

---

## Step 1: Read the Entire Input Artifact

Read the input artifact at `$ARGUMENTS` from start to finish before writing a single
line of code.

If `$ARGUMENTS` is a refined story:
1. Read the full story first.
2. Locate the companion plan reference inside the story.
3. Read that full plan before making changes.
4. If the story does not name a plan, or the reference is ambiguous, stop and
   report the missing contract instead of guessing.

Once the plan is identified, understand:

- All tasks and their dependencies
- Affected areas and files
- Architecture notes and constraints
- The validation steps at the end

Do NOT start implementing until you have the full picture.

---

## Step 2: Verify Current State

Check the working tree is clean before starting:

```bash
git status
```

If there are uncommitted changes unrelated to this plan, flag them before proceeding.

Check the current branch:

```bash
git branch --show-current
```

---

## Step 3: Execute Tasks in Dependency Order

Work through each task in the plan sequentially (respecting `Depends on:` ordering).

### For each task:

1. **Read** the target file(s) before modifying — never edit blindly.
2. **Implement** the change using the Edit or Write tools.
3. **Verify** the change works after each significant modification.
   Fix errors immediately — do not accumulate them.

### Project conventions:

Follow the project conventions specified in AGENTS.md and any on-demand rules
loaded from `.opencode/rules/` for the relevant area.

If AGENTS.md specifies project conventions (import style, error handling,
logging), follow them. If not, use standard practices for the project's
language and framework.

If execution started from a refined story, keep the story lot boundary as the
scope guard. Do not broaden the work beyond the story and companion plan.

---

## Step 4: Run Incremental Validation

After completing a group of related tasks, run the project's incremental
validation commands. Check AGENTS.md for the specific commands. If AGENTS.md is
absent, use the repo-local validation steps named in the plan. Common patterns:
type checking, linting, formatting, unit tests for the affected area.

Fix any failures before proceeding to the next task group.

---

## Step 5: Run Full Validation

After all tasks are complete, run the project's full validation suite as
specified in AGENTS.md. If AGENTS.md is absent, run the full validation named in
the plan or the closest repo-local equivalent.

All checks must pass. If any fail, fix them before reporting completion.

---

## Step 6: Output Report

Provide a structured completion report:

```
## Execution Report: {Plan Name}

### Tasks Completed
- [x] Task 1: {description} — {files changed}
- [x] Task 2: {description} — {files changed}
...

### Files Created
- `{path}` — {purpose}

### Files Modified
- `{path}` — {what changed}

### Validation Results
- {check 1}: PASS / FAIL
- {check 2}: PASS / FAIL
- Full validation: PASS / FAIL

### Manual Verification
{Any steps to manually verify the feature works.}

### Notes
{Any deviations from the plan, unexpected findings, or follow-up work needed.}
```
