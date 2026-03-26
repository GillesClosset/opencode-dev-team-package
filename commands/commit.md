---
description: Create an enriched commit for current changes (WISC Write pillar)
---

# Commit Changes

## Process

### 1. Review Changes

```bash
git status
git diff HEAD
git diff --stat HEAD
```

Check for new untracked files:
```bash
git ls-files --others --exclude-standard
```

### 2. Stage Files

Add the untracked and changed files relevant to the current work.

**Do NOT stage:**
- `.env` or credential files
- Large binary files
- Files unrelated to the current task

### 3. Create Commit

Write an atomic commit message with a conventional commit tag:

- `feat:` — New capability or feature
- `fix:` — Bug fix
- `refactor:` — Code restructure without behavior change
- `docs:` — Documentation only
- `test:` — Test additions or fixes
- `chore:` — Build, CI, tooling changes
- `perf:` — Performance improvement

**For changes spanning multiple areas**, note the primary area in the scope:
```
feat(api): add rate limiting middleware
fix(ui): resolve form validation on submit
refactor(auth): simplify token refresh flow
```

**Commit message format:**
```
tag(scope): concise description of what changed

[Optional body explaining WHY this change was made,
not just what changed. Include context that isn't
obvious from the diff.]

[Optional: Fixes #123, Closes #456]
```

### 4. Capture AI Context Changes

If any AI context assets were modified in this commit, add a `Context:` section to the commit body:

```
feat(auth): add retry logic for token refresh

Added exponential backoff when token refresh fails mid-session.
Previously a single failure would log the user out.

Context:
- Updated .opencode/rules/auth.md with retry conventions
- Added .opencode/commands/debug-session.md for session state inspection
- Surfaced issue: mock patterns in retry tests need isolated setup

Fixes #482
```

**What counts as AI context changes:**
- `.opencode/rules/` — on-demand conventions added, updated, or removed
- `.opencode/commands/` — slash commands created or modified
- `.opencode/docs/` — reference docs added or updated
- `AGENTS.md` — global rules changes

**Why this matters:** Your git log is long-term memory. Future agents and sessions use `git log` to understand project history. If context changes aren't captured in commits, the AI layer's evolution becomes invisible — you lose the ability to trace WHY a rule exists or WHEN a command was added.
