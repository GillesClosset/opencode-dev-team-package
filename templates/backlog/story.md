# Story Template

Use this template to create a backlog story at the end of a discovery session.
The story is the contract between the discovery specialist and the execution agent.
Cody reads it as the authoritative input for the delivery lot.

Copy this file, rename it to reflect the story (e.g. `E01S01-add-rate-limiting.md`),
and fill in every section before handing off to Cody.

---

## Story ID

`E__S__` — Epic number and story number within the epic. Assign sequentially.

## Title

One-line description of what gets built or fixed.

## Objective

One or two sentences. What problem does this solve, and for whom?

## Companion plan

Path to the detailed implementation plan for this lot.

`.opencode/plans/{kebab-case-name}.md`

## Lot boundary

Explicit list of what is in scope and what is explicitly out of scope.

**In scope:**
- ...

**Out of scope:**
- ...

## Acceptance criteria

Each criterion must be independently verifiable. Write them as checkboxes.

- [ ] ...
- [ ] ...
- [ ] ...

## Files likely touched

List the files or directories expected to change. This is an estimate, not a
guarantee. Cody should stay within this zone unless a blocking reason requires
going outside it (in which case, escalate).

- `path/to/file` — reason

## Dependencies

External libraries, APIs, or services this story touches. If documentation
accuracy is critical, note that external docs should be consulted before implementation.

- ...

## Risks and unknowns

- ...

## Status

`draft` | `refined` | `active` | `done` | `blocked`

Set to `refined` once the discovery specialist has validated that the acceptance
criteria are testable and the lot boundary is explicit enough for Cody to act on.

## Notes

Any additional context, links, or prior decisions relevant to this story.
Use this section for short execution handoff notes only — not a duplicate plan.
