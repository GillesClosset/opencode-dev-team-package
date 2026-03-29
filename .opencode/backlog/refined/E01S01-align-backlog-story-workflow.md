# Story Template

---

## Story ID

`E01S01`

## Title

Align backlog story workflow with planning and execution prompts.

## Objective

Make the new planning workflow executable end-to-end by ensuring discovery writes a refined backlog story alongside the detailed plan, and that main/Cody/docs all agree on how that lot is handed off.

## Lot boundary

Explicit list of what is in scope and what is explicitly out of scope.

**In scope:**
- Align prompt and documentation wording for plan output vs story output
- Standardize the refined story storage path and lifecycle language
- Add the minimum story metadata needed to reference a companion plan

**Out of scope:**
- New runtime automation outside markdown prompt/template changes
- Broad redesign of the WISC workflow beyond planning/backlog handoff
- Adding repository-wide automated tests or CI for markdown artifacts

## Acceptance criteria

- [ ] `/plan` guidance explicitly requires a detailed plan in `.opencode/plans/` and a refined story in `.opencode/backlog/refined/`
- [ ] `agents/discovery-architect.md`, `agents/main.md`, `agents/cody.md`, and `commands/execute.md` describe the same handoff contract
- [ ] `docs/daily-workflow.md` and `docs/onboarding-existing-project.md` describe the same backlog lifecycle and artifact locations
- [ ] `templates/backlog/story.md` includes a clear place to reference the companion plan

## Files likely touched

- `commands/plan.md` — define dual output contract
- `commands/execute.md` — accept story-backed execution handoff
- `agents/discovery-architect.md` — align backlog story instructions
- `agents/main.md` — align orchestration wording
- `agents/cody.md` — align delivery wording
- `docs/daily-workflow.md` — document the end-to-end flow
- `docs/onboarding-existing-project.md` — align installation/onboarding guidance
- `templates/backlog/story.md` — add plan reference metadata

## Dependencies

- No external libraries
- Internal dependency on the existing `.opencode/plans/` convention and backlog folder structure documented in onboarding

## Risks and unknowns

- There is no root `AGENTS.md` in this package repo, so validation guidance must stay repo-local
- The team may prefer either “story is primary” or “plan is primary”; this lot assumes a paired-artifact model to minimize churn

## Status

`refined`

## Notes

Companion plan: `.opencode/plans/backlog-story-plan-alignment.md`
