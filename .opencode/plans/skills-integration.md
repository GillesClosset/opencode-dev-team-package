# Plan: Skills Integration (5 Skills)

## Overview

Add 5 agent skills to the WISC package: `perplexity` (web research via CLI),
`frontend-ui` (component + chat interface patterns), `api-design` (REST/GraphQL/tRPC
conventions), `database` (schema design, indexing, migrations), and `security` (OWASP,
auth patterns, input validation). Each skill is a separate SKILL.md file. Skills are
assigned to specific agents based on role. Agent prompts, manifest, and docs are updated
to make skills first-class and explicitly actionable.

## Success Criteria

- [ ] `skills/perplexity/SKILL.md` exists — covers Perplexity CLI install + research workflow
- [ ] `skills/frontend-ui/SKILL.md` exists — covers component patterns + chat/streaming UI
- [ ] `skills/api-design/SKILL.md` exists — covers REST/GraphQL/tRPC conventions
- [ ] `skills/database/SKILL.md` exists — covers schema design, indexing, migrations
- [ ] `skills/security/SKILL.md` exists — covers OWASP, auth patterns, input validation
- [ ] `export-manifest.yaml` has a `skills:` section listing all 5 skills with install targets
- [ ] `agents/main.md` references perplexity + frontend-ui + api-design + database + security with explicit trigger conditions
- [ ] `agents/discovery-architect.md` references perplexity + frontend-ui + api-design + database + security
- [ ] `agents/cody.md` references frontend-ui + api-design + database + security (not perplexity — Cody works from plans)
- [ ] `agents/scout.md` references perplexity for external research
- [ ] `agents/qa-review.md` references perplexity + security
- [ ] `docs/installation.md` includes a "Install Skills" section
- [ ] `docs/customization.md` includes a "Skills" section explaining how to add new skills
- [ ] `README.md` surfaces skills in the package overview

## Affected Areas

- `skills/` (new top-level directory) — 5 new SKILL.md files
- `export-manifest.yaml` — new `skills:` section (5 entries)
- `agents/main.md` — add explicit skills section with trigger conditions per skill
- `agents/discovery-architect.md` — replace vague "Available skills" with explicit skill protocol
- `agents/cody.md` — replace vague "Available skills" with explicit skill protocol (no perplexity)
- `agents/scout.md` — add skills section (perplexity for external research)
- `agents/qa-review.md` — add skills section (perplexity + security)
- `docs/installation.md` — add "Install Skills" step
- `docs/customization.md` — add "Creating New Skills" section
- `README.md` — add skills to directory tree and add a Skills section

## Architecture Notes

**Skill-to-agent assignment (by role):**

| Skill | main | discovery-architect | cody | scout | qa-review |
|-------|------|---------------------|------|-------|-----------|
| perplexity | ✓ | ✓ | — | ✓ | ✓ |
| frontend-ui | ✓ | ✓ | ✓ | — | — |
| api-design | ✓ | ✓ | ✓ | — | — |
| database | ✓ | ✓ | ✓ | — | — |
| security | ✓ | ✓ | — | — | ✓ |

Rationale:
- `cody` gets domain skills (frontend, api, db) but NOT perplexity — Cody implements from the plan, not from live research.
- `scout` gets perplexity only — for researching external topics; it doesn't need domain design skills.
- `qa-review` gets perplexity (to validate claims against current practice) and security (to check against known vulnerability patterns).
- `main` + `discovery-architect` get all skills — they orchestrate and scope work across all domains.

**Perplexity via CLI (not MCP):**
Best practice is to encourage installing the Perplexity CLI rather than configuring MCP.
The skill should include install instructions (`npm install -g perplexity-cli` or the
current recommended method) and a usage workflow for running queries from the terminal.

**Skill file format:**
Follow the same frontmatter + body pattern as the existing `context7-mcp` skill:
```yaml
---
name: {skill-name}
description: {one-line trigger description}
---
```
Then: When to activate, How to use it, Key patterns/checklists.

**Install path convention:**
- Project-local: `.agents/skills/{name}/SKILL.md`
- Global: `~/.agents/skills/{name}/SKILL.md`
Package ships under `skills/{name}/SKILL.md`.

**Skills are orthogonal to the 4-layer system** — do not modify `docs/context-layers.md`.

## Implementation Tasks

### Task 1: Create skills/perplexity/SKILL.md
**File:** `skills/perplexity/SKILL.md`
**Type:** Create
**Description:** Skill for live web research using the Perplexity CLI. Include:
- Frontmatter: `name: perplexity`, description trigger
- When to activate: validating architectural decisions, comparing libraries, checking
  current best practices, researching security advisories, verifying API availability
- Install instructions: `npm install -g @perplexity-ai/cli` (or `npx perplexity`) — note
  that the user should verify the current package name as CLI tooling evolves
- Authentication: `perplexity auth` / `PERPLEXITY_API_KEY` env var
- Workflow: formulate a precise query → run `perplexity search "<query>"` → extract
  relevant findings → cite the source
- Guardrails: use for "is this current best practice?" not for implementation details
  (use context7 for library API specifics)
**Depends on:** none

### Task 2: Create skills/frontend-ui/SKILL.md
**File:** `skills/frontend-ui/SKILL.md`
**Type:** Create
**Description:** Skill for frontend component design and chat/streaming interface patterns. Include:
- Frontmatter: `name: frontend-ui`, description trigger
- When to activate: designing UI components, building chat interfaces, streaming response
  UIs, accessibility decisions, design system questions
- Component architecture patterns: composition over inheritance, controlled vs uncontrolled,
  co-locating state with the component that owns it
- Chat/streaming UI patterns specifically:
  - Message threading and scroll anchoring (scroll-to-bottom on new message, user scroll
    overrides auto-scroll)
  - Optimistic updates (show user message immediately before server confirmation)
  - Streaming token display (append tokens incrementally, avoid layout thrashing)
  - Loading/typing indicators
  - Error states and retry UX
- Accessibility checklist: keyboard navigation, ARIA roles for live regions, focus management
- Key questions to ask before implementing: is state local or shared, does it stream,
  what's the error recovery path
**Depends on:** none

### Task 3: Create skills/api-design/SKILL.md
**File:** `skills/api-design/SKILL.md`
**Type:** Create
**Description:** Skill for REST, GraphQL, and tRPC API design conventions. Include:
- Frontmatter: `name: api-design`, description trigger
- When to activate: designing new endpoints, reviewing API contracts, versioning decisions,
  error response standardization
- REST conventions: resource naming (nouns not verbs), HTTP verb semantics, status code
  standards (200/201/204/400/401/403/404/409/422/500), pagination patterns (cursor vs offset),
  idempotency keys for mutations
- Error response format: always return `{ error: { code, message, details? } }` — never
  expose stack traces in production
- Versioning: prefer URL versioning (`/v1/`) for public APIs, header versioning for internal
- GraphQL conventions: queries vs mutations vs subscriptions, N+1 problem (DataLoader),
  error handling in resolvers, schema-first vs code-first
- tRPC: procedure naming, input validation with Zod, error types
- Key questions: is this a public or internal API, does it need versioning, what's the
  pagination strategy, how are errors surfaced to the client
**Depends on:** none

### Task 4: Create skills/database/SKILL.md
**File:** `skills/database/SKILL.md`
**Type:** Create
**Description:** Skill for database schema design, indexing, and migration best practices. Include:
- Frontmatter: `name: database`, description trigger
- When to activate: designing new tables/collections, adding indexes, writing migrations,
  query performance questions, data modeling decisions
- Schema design principles: normalize to 3NF by default, denormalize deliberately with
  documented reason, use surrogate keys (UUID or serial), soft delete pattern
  (`deleted_at` timestamp vs hard delete tradeoffs)
- Indexing guidelines: index foreign keys, index columns used in WHERE/ORDER BY,
  composite index column order matters (most selective first), watch out for index
  bloat on write-heavy tables
- Migration best practices: always reversible (up + down), never drop columns in the
  same migration that removes code references, add nullable columns before populating,
  large table migrations need batching strategy
- Query patterns: avoid N+1 (use joins or eager loading), EXPLAIN/ANALYZE before
  optimizing, connection pooling awareness
- Key questions: what's the read/write ratio, does this need soft delete, what's the
  migration rollback plan, are there existing indexes to leverage
**Depends on:** none

### Task 5: Create skills/security/SKILL.md
**File:** `skills/security/SKILL.md`
**Type:** Create
**Description:** Skill for security review using OWASP patterns, auth best practices, and input validation. Include:
- Frontmatter: `name: security`, description trigger
- When to activate: reviewing auth flows, handling user input, file uploads, API endpoints,
  session management, any data that crosses a trust boundary
- OWASP Top 10 quick checklist (most commonly relevant):
  - Injection: parameterized queries only, never string interpolation in SQL/shell/LDAP
  - Broken auth: check token expiry, refresh token rotation, secure cookie flags
    (HttpOnly, Secure, SameSite)
  - Sensitive data exposure: no secrets in logs, no PII in URLs, encrypt at rest
  - IDOR (Insecure Direct Object Reference): always authorize, not just authenticate
  - Security misconfiguration: check CORS policy, CSP headers, disable directory listing
  - XSS: escape output, use framework sanitization, CSP as defense-in-depth
  - CSRF: SameSite cookies + CSRF token for state-changing requests
- Auth patterns: JWT (short-lived access + refresh), OAuth2/OIDC flow, API key hashing
  (store SHA-256 hash, never plaintext)
- Input validation: validate at the boundary (controller/resolver level), use schema
  validation (Zod/Joi/Pydantic), reject unknown fields
- Key questions: what trust boundary does this cross, who can call this endpoint, is
  authorization checked (not just authentication), is any input reflected in output
**Depends on:** none

### Task 6: Register all 5 skills in export-manifest.yaml
**File:** `export-manifest.yaml`
**Type:** Modify
**Description:** Add a `skills:` section after the `agents:` section. Each entry includes:
- `path:` the package-relative path
- `installs_to:` the project-local install target (`.agents/skills/{name}/SKILL.md`)
- `purpose:` one-line description
List all 5 skills: perplexity, frontend-ui, api-design, database, security.
**Depends on:** Tasks 1–5

### Task 7: Update agents/main.md — add explicit skills section
**File:** `agents/main.md`
**Type:** Modify
**Description:** Add a `## Skills` section near the end of the file (before `## Local customization`). Include:
- A table listing all 5 skills with their trigger conditions
- The instruction: "Load the relevant skill when the task domain matches — do not load all skills speculatively"
- Note that perplexity is for validating best practices and current state; domain skills
  (frontend-ui, api-design, database, security) are for design decisions in those areas
**Depends on:** Tasks 1–5

### Task 8: Update agents/discovery-architect.md — replace vague skills section
**File:** `agents/discovery-architect.md`
**Type:** Modify
**Description:** Replace the vague `## Available skills` section (lines 172–175) with an explicit
`## Skills` section that:
- Lists all 5 skills with explicit trigger conditions
- For perplexity: "Use in Phase 3 (External Research) to validate architectural choices
  against current best practices — run `perplexity search` before recommending an approach
  you are not confident is current"
- For domain skills: "Use during Phase 4 (Strategic Thinking) when the lot involves
  that domain — load the skill, apply its checklist to the architecture decision"
- Fallback rule: if a skill is unavailable, flag as a risk in the plan rather than guessing
**Depends on:** Tasks 1–5

### Task 9: Update agents/cody.md — replace vague skills section
**File:** `agents/cody.md`
**Type:** Modify
**Description:** Replace the vague `## Available skills` section (lines 126–131) with an explicit
`## Skills` section that:
- Lists frontend-ui, api-design, database, security with trigger conditions
- Does NOT include perplexity — Cody implements from the plan, not from live research
- For each: "Before implementing in this domain, load the skill and apply its checklist
  to verify your approach matches current conventions"
- Note: if a domain skill is unavailable, proceed with project conventions from AGENTS.md
  and on-demand rules
**Depends on:** Tasks 1–5

### Task 10: Update agents/scout.md — add skills section
**File:** `agents/scout.md`
**Type:** Modify
**Description:** Add a `## Skills` section at the end of the file. Scout gets perplexity only:
- When to use: when a research question involves current ecosystem state, library
  comparisons, or technology choices that may have changed since training data
- Workflow: formulate a precise query → run via perplexity CLI → include findings in
  the research summary with source attribution
- Note: for library API specifics, use context7-mcp if available (also list this)
**Depends on:** Task 1

### Task 11: Update agents/qa-review.md — add skills section
**File:** `agents/qa-review.md`
**Type:** Modify
**Description:** Add a `## Skills` section after `## Rules`. QA-review gets perplexity + security:
- Perplexity: use to validate that implementation follows current best practices —
  especially for auth flows, security patterns, and library version choices
- Security: apply the security skill checklist to any change that touches auth,
  user input handling, API endpoints, or data persistence
**Depends on:** Tasks 1, 5

### Task 12: Update docs/installation.md — add Install Skills step
**File:** `docs/installation.md`
**Type:** Modify
**Description:** Add a `## Install Skills` section after the Quick Install section. Include:
- Explanation: skills give agents access to specialized domain knowledge and live research
- Install commands for each skill (project-local and global options)
- Note about perplexity CLI setup (requires API key)
- Update the `## Verify the Installation` section to mention skills
**Depends on:** Tasks 1–5

### Task 13: Update docs/customization.md — add Creating New Skills section
**File:** `docs/customization.md`
**Type:** Modify
**Description:** Add a `## Creating New Skills` section at the end of the file. Include:
- What skills are (SKILL.md files loaded into agent context via OpenCode's skill system)
- Install path: `.agents/skills/{name}/SKILL.md`
- SKILL.md format: frontmatter (`name`, `description`) + body sections
- When to create a skill vs. a rule: skills are for tool/domain capabilities reusable
  across all projects; rules are for project-specific conventions
- When to create a skill vs. a doc: skills are active (agent follows a workflow);
  docs are passive reference material
**Depends on:** none (parallel to other tasks)

### Task 14: Update README.md — surface skills
**File:** `README.md`
**Type:** Modify
**Description:** Two targeted updates:
1. In the directory tree (if present), add `skills/` alongside `commands/`, `agents/`, etc.
2. Add a `## Skills` section listing the 5 bundled skills with one-line descriptions
   and their agent assignments
**Depends on:** Tasks 1–5

## Validation Steps

1. **File existence check:**
   ```bash
   ls skills/*/SKILL.md
   ```
   Should list 5 files: perplexity, frontend-ui, api-design, database, security.

2. **Manifest check:**
   ```bash
   grep -A 30 "skills:" export-manifest.yaml
   ```
   Should show all 5 entries.

3. **Agent coverage check:**
   ```bash
   grep -l "perplexity" agents/*.md
   ```
   Should match: main.md, discovery-architect.md, scout.md, qa-review.md (NOT cody.md)

4. **Cody safety check:**
   ```bash
   grep "perplexity" agents/cody.md
   ```
   Should return nothing.

5. **Docs consistency:**
   ```bash
   grep -rn "\.agents/skills" docs/ README.md export-manifest.yaml
   ```
   All references to the install path should be consistent.

6. **No content loss:** Every agent file's line count should be equal to or greater
   than the original (only additions, no deletions except the vague "Available skills"
   sections being replaced by richer "Skills" sections).

## Rollback Notes

All changes are additive or targeted replacements of vague placeholder sections:
- Delete `skills/` directory to remove all skill files
- Remove `skills:` block from `export-manifest.yaml`
- Revert agent files: re-replace the `## Skills` sections with the original vague text
- Remove added sections from docs and README

Nothing is deleted from the existing behavior. All agents continue to work without
skills installed — they fall back to training-data knowledge and project conventions.
