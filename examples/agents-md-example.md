# Example AGENTS.md

> This is an example of what a lean, effective AGENTS.md file looks like.
> Copy this to your project root and fill in the project-specific details.
> Keep it under 500 lines. If it grows past that, split content into
> `.opencode/rules/` files.

---

## Project Overview

{Project name} is a {1-sentence description of what it does and who it serves}.

## Tech Stack

- **Language:** {e.g. TypeScript 5.4, Python 3.12, Go 1.22}
- **Framework:** {e.g. Next.js 14, FastAPI, Gin}
- **Database:** {e.g. PostgreSQL 16, MongoDB 7}
- **Build:** {e.g. Vite, Webpack, Make}
- **Package manager:** {e.g. npm, pnpm, uv, cargo}
- **Test framework:** {e.g. Vitest, pytest, Go testing}

## Project Structure

```
{project-root}/
├── src/                    # {Main source code}
│   ├── api/                # {API routes and controllers}
│   ├── services/           # {Business logic}
│   ├── db/                 # {Database access layer}
│   ├── models/             # {Data models and types}
│   └── utils/              # {Shared utilities}
├── tests/                  # {Test files}
├── migrations/             # {Database migrations}
├── docs/                   # {Project documentation}
├── .opencode/              # {AI agent configuration}
│   ├── commands/           # {Slash commands}
│   ├── agents/             # {Agent definitions}
│   ├── rules/              # {On-demand zone rules}
│   ├── docs/               # {Reference docs with scout headers}
│   ├── plans/              # {Implementation plans}
│   ├── backlog/            # {Story artefacts}
│   └── handoffs/           # {Session handoff files}
└── AGENTS.md               # {This file — global rules}
```

## Key Commands

```bash
# Development
{dev command, e.g. npm run dev}

# Testing
{test command, e.g. npm test}

# Linting
{lint command, e.g. npm run lint}

# Type checking (if applicable)
{type check command, e.g. npm run type-check}

# Build
{build command, e.g. npm run build}

# Full validation (all checks)
{validate command, e.g. npm run validate}
```

## Architecture

{5-10 lines describing the high-level architecture. Key modules, data flow
direction, main entry points. This should orient an agent quickly without
going deep — deep dives belong in `.opencode/docs/`.}

## Conventions

- **Imports:** {e.g. named imports, no namespace imports, type-only imports for types}
- **Error handling:** {e.g. typed errors, never swallow silently, log + rethrow}
- **Logging:** {e.g. structured logging with pino, lazy logger pattern}
- **Commit format:** Conventional commits: `type(scope): description`
- **Branch model:** {e.g. feature branches → PR → squash merge to main}

## High-Risk Zones

Do not modify these areas without explicit authorization:
- `{path/to/migrations/}` — Database migrations (irreversible in production)
- `{path/to/auth/}` — Authentication and authorization
- `{path/to/ci/}` — CI/CD configuration

## On-Demand Rules

Zone-specific conventions are in `.opencode/rules/`. Load the relevant
rule file when entering a specific area:

- `frontend.md` — UI component patterns, styling, data fetching
- `api.md` — Route patterns, middleware, validation
- `database.md` — Query patterns, migrations, schema conventions
- `testing.md` — Test structure, mocking, isolation
