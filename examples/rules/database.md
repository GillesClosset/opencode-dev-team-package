# Database Conventions

> **When to use:** Load this rule file at the start of any session working
> on the database layer (queries, migrations, schema changes).
>
> **WISC layer:** L2 — On-demand rules. This file should NOT be in AGENTS.md
> (that's L1, always-loaded). It loads only when the work enters this zone.

## Stack

- {Database, e.g. PostgreSQL 16, MySQL 8, SQLite}
- {ORM/Query builder, e.g. Prisma, Drizzle, SQLAlchemy, Knex}
- {Migration tool, e.g. built-in ORM migrations, Flyway, golang-migrate}

## Schema Conventions

{Describe table naming (snake_case, plural), column naming, primary key
strategy (UUID, auto-increment), timestamp columns (created_at, updated_at),
soft delete patterns.}

## Query Patterns

{Describe how queries are structured. Examples: repository pattern,
data access objects, direct ORM calls. Note parameterized query
conventions to prevent SQL injection.}

```
{Example query pattern:
const result = await db.query(
  'SELECT * FROM {table} WHERE id = $1',
  [id]
);
}
```

## Migration Conventions

{Describe how migrations are created, named, and run. Note any
conventions for reversible migrations, data migrations vs. schema
migrations, and migration testing.}

```bash
# Example migration commands
{migration create command}
{migration run command}
{migration rollback command}
```

## Key Files

- `{path/to/db/connection}` — Database connection setup
- `{path/to/db/queries/}` — Query modules or repositories
- `{path/to/migrations/}` — Migration files
- `{path/to/db/types}` — Database type definitions

## Testing

{How to test database code. Patterns for test database setup,
fixtures, transaction rollback after tests, mocking vs. real DB.}

## Anti-patterns

- {e.g. Never use string interpolation in SQL — always use parameterized queries}
- {e.g. Never SELECT * in production queries — select specific columns}
- {e.g. Never bypass the data access layer from application code}
- {e.g. Never assume query results are non-empty — always null-check}
- {e.g. Never write irreversible migrations without explicit approval}
