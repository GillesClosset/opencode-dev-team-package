# Plan: Align Backlog Story Workflow with Planning and Execution

## Overview
Add the missing story-contract workflow around `/plan` so discovery produces both a detailed execution plan and a refined backlog story, and execution/documentation paths agree on where those artifacts live and how they are consumed.

## Success Criteria
- [ ] `/plan` guidance clearly requires saving the detailed plan in `.opencode/plans/` and a companion refined story in `.opencode/backlog/refined/`
- [ ] Discovery, main, and execution agent prompts agree on whether Cody executes from a plan, a story, or a story that references a plan
- [ ] Workflow docs and onboarding docs consistently describe the same backlog lifecycle (`refined` → `active` → `done` / `blocked`)
- [ ] Template and prompt wording are aligned enough that an execution agent can pick up the generated artifact without redoing discovery
- [ ] Passes project validation suite

## Affected Areas
- `commands/plan.md` — define the dual-artifact output and naming/location rules
- `commands/execute.md` — clarify accepted input contract for plan files vs story files
- `agents/discovery-architect.md` — align plan output protocol with backlog story storage and handoff behavior
- `agents/main.md` — align orchestration guidance with the backlog-first handoff path
- `agents/cody.md` — clarify how delivery consumes a story-backed lot and updates status safely
- `docs/daily-workflow.md` — document the real end-to-end discovery → backlog → execution flow
- `docs/onboarding-existing-project.md` — align setup guidance with actual artifact locations and lifecycle
- `templates/backlog/story.md` — tighten the story contract so it can reference a companion plan cleanly

## Architecture Notes
Keep the current separation of concerns: the plan remains the detailed implementation document in `.opencode/plans/`, while the story is the durable lot contract stored in `.opencode/backlog/refined/`. The safest change is to make `/plan` produce both artifacts and make execution guidance explicitly accept either a direct plan path or a story that points to its companion plan. This avoids collapsing story and plan into one file while fixing the current documentation/prompt mismatch. Because this repository is a prompt package and has no root `AGENTS.md`, validation must use repo-local consistency checks rather than AGENTS-driven commands.

## Implementation Tasks

### Task 1: Define the canonical artifact flow in plan command
**File:** `commands/plan.md`
**Type:** Modify
**Description:** Update the `/plan` command contract so it explicitly requires two outputs for non-trivial work: a detailed plan in `.opencode/plans/{kebab-case}.md` and a refined story in `.opencode/backlog/refined/{story-id}-{kebab-case}.md`. Add naming, linkage, and output expectations so the command itself matches the discovery agent behavior.
**Depends on:** none

### Task 2: Align discovery agent output protocol with backlog storage
**File:** `agents/discovery-architect.md`
**Type:** Modify
**Description:** Reconcile the discovery prompt with the new canonical flow: plan file for execution detail, story file for lot handoff, explicit `refined` status, and `.opencode/backlog/refined/` as the default destination. Remove ambiguity about whether the story is optional once a lot is ready.
**Depends on:** Task 1

### Task 3: Make execution contract story-aware without broadening scope
**File:** `commands/execute.md`
**Type:** Modify
**Description:** Update `/execute` so it can safely consume either a plan file directly or a refined story that names its companion plan. Keep Cody’s implementation flow unchanged once the plan has been identified.
**Depends on:** Task 1

### Task 4: Update delivery and orchestration prompts to use the same handoff model
**File:** `agents/main.md`
**Type:** Modify
**Description:** Adjust orchestration guidance so the main agent hands Cody a story or plan consistently, and update Cody’s prompt so story-backed execution and status updates are explicitly supported without inventing extra workflow.
**Depends on:** Tasks 2-3

### Task 5: Tighten the backlog story template for plan linkage
**File:** `templates/backlog/story.md`
**Type:** Modify
**Description:** Add the minimum metadata needed for a refined story to point at its detailed plan (for example, a plan reference or execution reference section) while preserving the template as a concise contract.
**Depends on:** Task 1

### Task 6: Bring workflow and onboarding docs into alignment
**File:** `docs/daily-workflow.md`
**Type:** Modify
**Description:** Update the daily workflow to show discovery creating both artifacts and execution starting from the refined backlog item or referenced plan.
**Depends on:** Tasks 1-5

### Task 7: Align onboarding guidance with the real backlog lifecycle
**File:** `docs/onboarding-existing-project.md`
**Type:** Modify
**Description:** Ensure onboarding explains where refined stories live, how they move through backlog states, and how the plan/story pair is used in the first end-to-end cycle.
**Depends on:** Tasks 1-5

## Validation Steps
1. Verify path consistency with content search: confirm `.opencode/backlog/refined/` and `.opencode/plans/` references are intentional and non-conflicting across `commands/`, `agents/`, `docs/`, and `templates/`.
2. Manual test: walk a sample lot from `/plan "add X"` to `/execute ...` and confirm the prompts now describe one unambiguous flow, including where the story is saved and how Cody locates the plan.
3. Manual test: confirm the story template contains enough information for Cody or the main agent to identify the companion plan without redoing discovery.

## Rollback Notes
All changes are prompt/docs/template edits with no runtime code. Revert the touched markdown files to restore the previous plan-only flow if the dual-artifact contract proves too heavy. No data migration or irreversible state change is involved.
