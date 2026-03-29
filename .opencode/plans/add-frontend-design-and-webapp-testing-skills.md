# Plan: Add Frontend Design and Webapp Testing Skills

## Overview
Add two reusable skills to the WISC package: `frontend-design`, adapted from Anthropic's frontend-design guidance as an aesthetic complement to `frontend-ui`, and `webapp-testing`, a Playwright-based testing skill inspired by Anthropic's webapp-testing workflow. Update manifests, agent skill tables, and installation/customization docs so both skills are first-class, discoverable, and installable.

## Success Criteria
- [ ] `skills/frontend-design/SKILL.md` exists and clearly complements `frontend-ui` by focusing on aesthetic direction, visual distinctiveness, and production-grade design decisions rather than component architecture
- [ ] `skills/webapp-testing/SKILL.md` exists and provides a Playwright-based workflow for validating local web apps, including selector strategy, waits/assertions, debugging artifacts, and safe usage guidance
- [ ] `export-manifest.yaml` registers both new skills with correct install targets under `.agents/skills/`
- [ ] Agent prompts reference the new skills only where they fit the role: `frontend-design` for design-focused work and `webapp-testing` for browser-based validation work
- [ ] `README.md`, `docs/installation.md`, and `docs/customization.md` consistently describe the package as shipping 7 skills and explain how to install/use the two new ones
- [ ] Passes available repo consistency checks (`git diff --check`, file existence, and grep-based coverage checks); no `AGENTS.md`-defined validation suite currently exists in this repo

## Affected Areas
- `skills/frontend-design/` — new skill focused on aesthetic direction and anti-generic UI guidance
- `skills/webapp-testing/` — new skill focused on Playwright-based browser validation workflows
- `export-manifest.yaml` — register both skills for package export/install
- `agents/main.md` — advertise both skills with trigger conditions for orchestration
- `agents/discovery-architect.md` — add both skills for planning-time design/testing decisions
- `agents/cody.md` — add both skills for implementation-time design/testing guidance
- `agents/qa-review.md` — add `webapp-testing` for browser verification during review
- `README.md` — update bundled skill count and descriptions
- `docs/installation.md` — update install commands and dependency notes
- `docs/customization.md` — keep skill creation guidance aligned with the expanded catalog

## Architecture Notes
- **Keep the two skills narrow and non-overlapping:**
  - `frontend-ui` stays responsible for component architecture, state ownership, chat/streaming UX, and accessibility.
  - `frontend-design` should cover aesthetic direction, typography, color, motion, composition, and “avoid generic AI UI” guidance.
  - `webapp-testing` should cover browser-driven validation workflows, not general unit/integration testing conventions.
- **Adapt, do not copy verbatim:** Anthropic's `frontend-design` plugin is a strong content source, but the WISC version should match the existing `SKILL.md` style and agent-trigger format.
- **Do not port helper-script assumptions into this repo:** the Anthropic-inspired `webapp-testing` skill should rely on project-local Playwright setups and generic browser workflows. Bundling helper scripts (for server orchestration or screenshots) is out of scope for this lot.
- **Playwright guidance should reflect current docs:** prefer locators, role/text-based selectors, web-first assertions, auto-waiting, per-test isolation, and trace/screenshot debugging over brittle sleeps or DOM-coupled selectors.
- **Agent assignment recommendation:**
  - `frontend-design` → `main`, `discovery-architect`, `cody`
  - `webapp-testing` → `main`, `discovery-architect`, `cody`, `qa-review`
- **Documentation and code move together:** because this repo is a package of prompts/skills, new skill files without manifest/agent/docs updates would leave the feature only partially usable.

## Implementation Tasks

### Task 1: Create `frontend-design` skill
**File:** `skills/frontend-design/SKILL.md`
**Type:** Create
**Description:** Add a new skill in the house `SKILL.md` format that adapts Anthropic's frontend-design guidance into WISC conventions. Emphasize bold aesthetic direction, typography, color/theme cohesion, motion, spatial composition, and explicit anti-generic design guardrails. Make its boundary clear versus `frontend-ui`.
**Depends on:** none

### Task 2: Create `webapp-testing` skill
**File:** `skills/webapp-testing/SKILL.md`
**Type:** Create
**Description:** Add a Playwright-based testing skill inspired by Anthropic's webapp-testing workflow, but tailored to this package. Include trigger conditions, a reconnaissance-then-action workflow, guidance on using locators and web-first assertions, waiting for rendered state, capturing screenshots/traces/logs, and avoiding brittle selectors or blind sleeps.
**Depends on:** none

### Task 3: Register both skills in the export manifest
**File:** `export-manifest.yaml`
**Type:** Modify
**Description:** Add manifest entries for `frontend-design` and `webapp-testing` in the existing `skills:` section with `.agents/skills/{name}/SKILL.md` install targets and one-line purposes matching the new skill boundaries.
**Depends on:** Tasks 1-2

### Task 4: Update main agent skill routing
**File:** `agents/main.md`
**Type:** Modify
**Description:** Extend the `## Skills` table so orchestration can route frontend aesthetic work to `frontend-design` and browser-validation work to `webapp-testing`. Preserve the existing “load only what matches” guidance.
**Depends on:** Tasks 1-2

### Task 5: Update discovery-architect skill guidance
**File:** `agents/discovery-architect.md`
**Type:** Modify
**Description:** Add both skills to the discovery agent's skill table. Position `frontend-design` for Phase 4 design-direction decisions and `webapp-testing` for defining browser-verification strategy or acceptance checks for UI-heavy lots.
**Depends on:** Tasks 1-2

### Task 6: Update Cody skill guidance
**File:** `agents/cody.md`
**Type:** Modify
**Description:** Add `frontend-design` and `webapp-testing` to Cody's domain skill list. `frontend-design` should be used before implementing visually important frontend work; `webapp-testing` should be used before validating UI behavior through a browser workflow.
**Depends on:** Tasks 1-2

### Task 7: Update QA-review skill guidance
**File:** `agents/qa-review.md`
**Type:** Modify
**Description:** Add `webapp-testing` to QA-review so browser-level verification is an explicit part of review for frontend changes. Keep the section scoped to review-time validation rather than implementation.
**Depends on:** Task 2

### Task 8: Update README skill inventory
**File:** `README.md`
**Type:** Modify
**Description:** Change bundled skill counts from 5 to 7, add one-line descriptions for `frontend-design` and `webapp-testing`, and keep the directory tree and Skills section aligned with actual package contents and agent assignments.
**Depends on:** Tasks 1-2

### Task 9: Update installation guide
**File:** `docs/installation.md`
**Type:** Modify
**Description:** Expand install examples from 5 to 7 skills, add `frontend-design` and `webapp-testing`, and note that `webapp-testing` depends on a project-local Playwright/browser setup while the skill itself remains just guidance. Update verification text accordingly.
**Depends on:** Tasks 1-3

### Task 10: Update customization guide
**File:** `docs/customization.md`
**Type:** Modify
**Description:** Keep the skill-creation documentation aligned with the new catalog. Add brief examples that distinguish aesthetic/design skills from browser-testing skills so future contributors preserve narrow skill boundaries.
**Depends on:** Tasks 1-2

## Validation Steps
1. Confirm new skills exist:
   ```bash
   ls skills/*/SKILL.md
   ```
   Should now include `frontend-design` and `webapp-testing` in addition to the existing 5 skills.
2. Confirm manifest coverage:
   ```bash
   grep -n "frontend-design\|webapp-testing" export-manifest.yaml
   ```
3. Confirm agent coverage:
   ```bash
   grep -n "frontend-design\|webapp-testing" agents/*.md
   ```
   `frontend-design` should appear in `main.md`, `discovery-architect.md`, and `cody.md`; `webapp-testing` should appear in those files plus `qa-review.md`.
4. Confirm docs/readme consistency:
   ```bash
   grep -n "7 skills\|frontend-design\|webapp-testing" README.md docs/installation.md docs/customization.md
   ```
5. Run whitespace/conflict sanity check:
   ```bash
   git diff --check
   ```
6. Manual review: read the two new skills and verify the boundaries are explicit:
   - `frontend-design` complements rather than duplicates `frontend-ui`
   - `webapp-testing` is Playwright/browser-focused and does not imply bundled helper scripts that this repo does not ship

## Rollback Notes
This lot is fully reversible and low-blast-radius:
- Delete `skills/frontend-design/` and `skills/webapp-testing/`
- Remove their entries from `export-manifest.yaml`
- Remove their references from `agents/*.md`, `README.md`, and `docs/*.md`

No runtime codepaths or APIs are changed; rollback is a straightforward content reversion.
