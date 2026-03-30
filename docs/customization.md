# Customization Guide

How to extend the WISC package for your project.

---

## Creating Zone-Specific Prime Commands

Prime commands orient the agent on a specific part of the codebase before
starting work. The default `/prime` covers the whole project; zone-specific
variants go deeper.

1. Copy an example from `examples/prime-commands/`:
   ```bash
   cp /path/to/wisc-opencode/examples/prime-commands/prime-backend.md .opencode/commands/
   ```

2. Edit the file to match your project's structure:
   - Replace `{path/to/...}` placeholders with actual paths
   - Add project-specific entry points and key files
   - Adjust the output summary to cover what matters in your zone

3. The command will appear as `/prime-backend` in OpenCode

**Naming convention:** `prime-{zone}.md` → `/prime-{zone}`

---

## Creating On-Demand Rules

On-demand rules (L2) capture zone-specific conventions that should load
only when working in that area.

1. Start from the template:
   ```bash
   cp .opencode/rules/_template.md .opencode/rules/{zone}.md
   ```

2. Fill in each section:
   - **Conventions:** Patterns and standards for this area
   - **Key files:** The most important files and what they do
   - **Testing:** How to test changes, specific commands, gotchas
   - **Anti-patterns:** What to avoid

3. Reference from AGENTS.md:
   ```markdown
   ## On-Demand Rules
   Zone rules are in `.opencode/rules/`. Load the relevant file when
   entering a specific area.
   ```

**One file per zone.** Keep each under 300 lines. If a rule file grows too
large, the zone is probably too broad — split it.

---

## Creating Reference Docs

Reference docs (L3) are heavy documentation that loads via scout assessment.

1. Start from the template:
   ```bash
   cp .opencode/docs/_template.md .opencode/docs/{topic}.md
   ```

2. **Always include the 3-line scout header:**
   ```markdown
   > **Purpose:** {1 sentence — what this document covers}
   > **When to use:** {1 sentence — what kind of work makes this doc relevant}
   > **Size:** ~{N} lines — use the scout agent to check relevance before loading.
   ```

3. Write the full document content below the header.

**The scout header is critical.** Without it, the scout agent cannot assess
relevance, and the document will either be loaded speculatively (wasting
context) or never loaded (wasting the documentation effort).

---

## Customizing Commands

Each command in `.opencode/commands/` can be modified for your project.
Common customizations:

### `/commit`
- Add project-specific scope conventions:
  ```
  Scopes for this project: api, ui, db, auth, ci, docs
  ```
- Add ticket reference conventions:
  ```
  Always include the Jira ticket: feat(api): add rate limiting [PROJ-123]
  ```

### `/plan`
- Add project-specific research areas for Phase 2:
  ```
  Scout E — Check the migration history for schema changes related to this feature
  ```
- Add project-specific constraints for Phase 4

### `/execute`
- Add project-specific validation commands:
  ```
  After each task group: npm run type-check && npm run lint
  Full validation: npm run validate
  ```

### `/prime`
- Add project-specific entry points and structure
- Add checks for project-specific state (e.g., Docker containers, env files)

---

## Creating New Commands

Any `.md` file in `.opencode/commands/` becomes a slash command. The filename
(without `.md`) becomes the command name.

**Common additions:**

### `/review` — Trigger a QA review
```yaml
---
description: Review current changes for quality and risk
agent: qa-review
---
```

### `/test` — Run focused tests
```yaml
---
description: Run tests for a specific area
---
```

### `/deploy` — Deployment checklist
```yaml
---
description: Pre-deployment checklist and verification
---
```

**Frontmatter options:**
- `description:` — Shown in the command list (required)
- `agent:` — Auto-routes to a specific agent (optional)

---

## Customizing Agents

Agent files in `.opencode/agents/` can be modified to better fit your project.
Common customizations:

- Add project-specific safety rules to Cody
- Add domain knowledge to discovery-architect
- Adjust QA review criteria for your risk tolerance
- Add specialized knowledge areas to the scout agent

**Caution:** Keep agent changes minimal. The agents are designed to be
generic and derive project-specific behavior from AGENTS.md and on-demand
rules. If you find yourself adding lots of project-specific content to an
agent, it probably belongs in a rule file instead.

---

## Creating New Skills

Skills are `SKILL.md` files that give agents specialized domain knowledge or tool access.
Unlike rules (project-specific conventions) and docs (passive reference material), skills
are **active** — the agent follows a workflow or applies a checklist defined in the skill.

### When to create a skill vs. a rule

| | Skills | Rules |
|-|--------|-------|
| **Scope** | Reusable across all projects | Project-specific conventions |
| **Content** | Tool workflows, domain checklists, external service patterns | Coding standards, file structure, testing commands for this project |
| **Example** | "How to validate inputs using OWASP patterns" | "In this project, all API inputs use Zod schemas in `src/validators/`" |

### When to create a skill vs. a doc

| | Skills | Docs (L3) |
|-|--------|-----------|
| **Nature** | Active — agent follows a workflow | Passive — reference material the agent reads |
| **Usage** | Agent checks a checklist, runs a CLI, applies a pattern | Agent reads to understand architecture or data flows |
| **Example** | Security skill with OWASP checklist | Architecture deep-dive with system diagrams |

### Install path

Skills install to:
- Project-local: `.agents/skills/{name}/SKILL.md`
- Global: `~/.agents/skills/{name}/SKILL.md`

The package ships them under `skills/{name}/SKILL.md`.

Current bundled catalog: 6 skills — `frontend-ui`, `frontend-design`, `api-design`, `database`, `security`, and `webapp-testing`.

### SKILL.md format

Follow this structure:

```markdown
---
name: {skill-name}
description: {one-line trigger description — when should an agent load this skill?}
---

# {Skill Name} — {Short Tagline}

## When to activate

- {condition 1}
- {condition 2}

## {Main content section}

{Checklists, workflows, patterns, commands}

## Key questions before implementing

1. {Question that should be answered before using this skill}
```

**Frontmatter fields:**
- `name:` — machine-readable identifier (matches directory name)
- `description:` — shown to agents as the trigger condition; keep it one sentence and action-oriented

### Guidelines

- **Keep skills focused** — one domain or tool per skill file. Don't create a "backend" skill that covers APIs + databases + auth; split those into separate skills.
- **Preserve narrow boundaries** — e.g. use a design skill for aesthetic direction (`frontend-design`) and a separate testing skill for browser validation workflows (`webapp-testing`).
- **Include trigger conditions** — the "When to activate" section is critical. Agents use it to decide whether to load the skill.
- **Prefer checklists over prose** — agents apply checklists reliably; long prose sections get skimmed.
- **Keep external tool instructions current** — CLI tools evolve. Note where users should verify install instructions rather than hardcoding potentially stale commands.
- **Size:** aim for 100–300 lines. A skill that exceeds 400 lines is probably covering too much — split it.

### Boundary examples

- **Aesthetic direction skill:** focuses on visual hierarchy, typography, color, motion, and how to avoid a generic-looking UI.
- **Browser testing skill:** focuses on Playwright workflows, locator choice, web-first assertions, waits, and debugging artifacts.
- **Not a good combined skill:** one file that tries to cover visual design, component architecture, and browser testing together.
