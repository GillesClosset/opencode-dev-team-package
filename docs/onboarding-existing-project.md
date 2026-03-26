# Onboarding an Existing Project

This document describes how to deploy the WISC package into a project that
already has code, conventions, and possibly existing agents. The goal is a
clean integration that does not break what is already working.

## Why existing projects are different

A fresh environment has no friction. An existing project has:
- conventions already established (commit style, branch model, PR flow),
- tests and CI that must not regress,
- possibly agents or AGENTS.md files already in place,
- team members who did not choose this package and should not be surprised by it.

Rushing the setup creates agents that ignore real conventions and produce PRs that
do not match the project's expectations. The five phases below prevent that.

---

## Phase 1 — Audit before touching anything

Read before writing. The goal is to understand what exists so nothing is overwritten
and friction points are identified upfront.

**Checklist:**

- [ ] Does an `AGENTS.md` already exist? What does it say?
- [ ] Are there existing agents in `.opencode/agents/` or `~/.config/opencode/agents/`?
      List them. Do any overlap with the roles in this package (delivery, discovery, QA)?
- [ ] Are there existing commands in `.opencode/commands/`?
- [ ] What are the project's test commands? (`npm test`, `pytest`, `make test`, etc.)
- [ ] What is the branch model? (`main`, `develop`, feature branches, PR-based?)
- [ ] Are there commit conventions already in use? (Conventional Commits, custom
      prefix, ticket references?)
- [ ] Is there a PR template or review checklist?
- [ ] Which files or directories are high-risk / should not be touched carelessly?
      (migrations, shared config, auth, CI files)
- [ ] Is there existing documentation that agents should be aware of?
      (architecture docs, ADRs, API contracts)

Do not create any agents until this audit is complete. Write down the answers — you
will need them in Phase 2.

---

## Phase 2 — Adapt the package to the project

The commands and agents in this package are intentionally generic. That is a feature
for portability, but it means they need project-specific context to work well.

**Write your AGENTS.md:**
- Use `examples/agents-md-example.md` as a starting point
- Fill in: project overview, tech stack, structure, key commands, conventions
- Keep it under 500 lines (L1 — always loaded)
- Point to `.opencode/rules/` for zone-specific details

**Create your first on-demand rule:**
- Pick your project's most common work zone
- Copy the matching example from `examples/rules/`
- Fill in project-specific conventions, key files, testing patterns
- Save to `.opencode/rules/{zone}.md`

**Create your first reference doc (if applicable):**
- If you have architecture documentation, format it with the scout header
- See `scaffold/docs/_template.md` for the header format
- Save to `.opencode/docs/{topic}.md`

---

## Phase 3 — Install and validate one agent at a time

Do not install all agents at once and hope for the best. Follow the
installation steps in `docs/installation.md`, then validate each agent:

**Recommended sequence:**

1. **Scout first.** Run `/prime` and verify it produces a useful project summary.
   This validates that the scout agent works and can navigate your codebase.

2. **Main agent second.** Have a short conversation about a real (small, non-urgent)
   task. Verify it restates the problem correctly, identifies the right scope,
   and does not try to implement immediately.

3. **Cody third.** Delegate a small, low-risk task. Verify it reads the right
   files, stays in scope, produces a commit message that matches the project's
   conventions, and does not touch unrelated files.

4. **Discovery-architect fourth.** Give it a fuzzy problem. Verify it produces a
   bounded plan or story and escalates when the problem is unclear rather than
   guessing.

5. **QA specialist last.** Ask it to review a small recent change. Verify it returns
   a structured verdict and distinguishes real defects from preferences.

If any agent behaves unexpectedly, fix the prompts before moving to the next one.

---

## Phase 4 — Create the backlog infrastructure

This is the step most often skipped — and the one most likely to cause silent failure
later. Without a physical backlog, the contract between discovery and delivery stays
verbal. Verbal contracts break across sessions.

**What to create:**

```
your-project/
└── .opencode/
    └── backlog/
        ├── refined/       ← Stories ready for Cody, not yet started
        ├── active/        ← Stories currently being worked on
        ├── done/          ← Merged and closed stories (archive)
        └── blocked/       ← Stories waiting on a dependency or decision
```

**How it works in practice:**

1. Discovery session ends → discovery-architect writes a story using the
   story template and saves it to `.opencode/backlog/refined/`.
2. Main agent hands off to Cody by pointing at that file.
3. Cody reads the story, implements the lot, and marks the story status as `done`.
4. Main agent moves the file to `.opencode/backlog/done/` after merge.

The story file survives session restarts. If the session is interrupted mid-delivery,
the next session picks up from the file — not from a reconstructed verbal summary.

---

## Phase 5 — First real cycle on a small, low-risk lot

Before using the setup on anything important, run one full end-to-end cycle on a
task that is small and low-risk: a documentation fix, a small refactor, a minor bug.

**The cycle:**

1. Run `/prime` to verify the scout agent works and produces a useful summary.
2. Describe the task to the main agent.
3. Main agent delegates to discovery-architect.
4. Discovery-architect produces a plan or story in `.opencode/backlog/refined/`.
5. Main agent delegates to Cody with the plan/story file as input.
6. Cody implements, produces a commit, returns a structured report.
7. If risk warrants it, main agent delegates to QA specialist.
8. Main agent synthesizes back to you: what changed, what risk remains, next step.

This is your integration test. If anything breaks, it breaks on something cheap.
Fix it before the setup touches critical code.

---

## Common pitfalls on existing projects

**Existing AGENTS.md already has conventions.**
Do not overwrite it silently. Merge the WISC conventions into what is already
there, or create a separate file and reference it.

**Another agent already covers Cody's role.**
If a delivery agent already exists, adapt its prompt using `agents/cody.md` as a
reference rather than creating a duplicate.

**No test commands in AGENTS.md.**
Cody will invent plausible-sounding commands. It will be wrong. Always fill in
the actual test commands.

**Skipping the backlog folder.**
Without it, stories are passed as inline prompt text. They disappear at session
end. A blocked lot has no recovery point. The backlog folder is cheap to create
and expensive to skip.

**No on-demand rules created.**
Everything stays in AGENTS.md, which grows past 500 lines and becomes a
distractor. Split zone-specific content into `.opencode/rules/` files early.

**No reference docs with scout headers.**
Large docs loaded directly into main context cause context rot. Add the 3-line
scout header to any document over 200 lines.

**Running a large lot first.**
Always validate with a small lot before trusting the setup on anything with
meaningful regression risk.
