---
name: database
description: Use this skill when designing database schemas, adding indexes, writing migrations, or optimizing queries — covers SQL schema design, indexing guidelines, and migration best practices.
---

# Database — Schema Design, Indexing, and Migrations

## When to activate

Load this skill when:
- Designing new tables or collections
- Reviewing an existing schema for correctness or performance
- Adding or reviewing indexes
- Writing or reviewing database migrations
- Investigating slow queries or N+1 issues
- Making data modeling decisions (normalization, soft delete, relationships)

## Key questions before designing

1. What is the read/write ratio for this data? (Affects index strategy and normalization tradeoffs.)
2. Does this data need soft delete? (Audit trail vs hard delete tradeoffs.)
3. What is the migration rollback plan if this deployment fails?
4. Are there existing indexes on the table that can be leveraged?
5. Will this migration affect a large table in production? (Batching strategy needed?)

## Schema design principles

### Normalization
- Normalize to **3NF by default**: every non-key attribute depends on the whole key and nothing but the key.
- Denormalize deliberately and document the reason when performance requires it (e.g., a denormalized `order_total` column).
- Avoid storing derived data unless the derivation is expensive and frequently needed.

### Keys
- Use **surrogate keys**: UUID (`gen_random_uuid()`) or serial/autoincrement integers — not natural keys as primary keys.
- UUIDs are portable and safe to expose in URLs; integers are smaller and faster for joins.
- Use natural keys as unique constraints, not primary keys: `UNIQUE(email)` on the users table.

### Soft delete
- **Soft delete:** Add a `deleted_at TIMESTAMPTZ` column. Filter `WHERE deleted_at IS NULL` in all queries.
  - Pro: preserves audit trail, foreign key integrity, easy recovery.
  - Con: all queries must filter deleted records; indexes must account for it.
- **Hard delete:** Removes the row permanently.
  - Pro: simple, no filter overhead.
  - Con: no recovery, may break foreign keys if not cascaded.
- Choose soft delete when audit requirements exist or recovery may be needed.

### Naming conventions
- Tables: `snake_case`, plural (`users`, `order_items`).
- Columns: `snake_case`, singular (`user_id`, `created_at`).
- Foreign keys: `{referenced_table_singular}_id` (e.g., `user_id` references `users.id`).
- Timestamps: `created_at`, `updated_at`, `deleted_at` — always `TIMESTAMPTZ` (timezone-aware).

## Indexing guidelines

### When to index
- **Always index foreign keys** — joins and cascade deletes depend on them.
- Index columns used in `WHERE` clauses that filter a significant portion of rows.
- Index columns used in `ORDER BY` when the result set is large.
- Consider partial indexes for frequently filtered subsets: `WHERE deleted_at IS NULL`.

### Composite index column order
- **Most selective column first** — the column that filters out the most rows goes first.
- Order should match common query patterns: if you always query `WHERE user_id = ? AND status = ?`, use `(user_id, status)`.
- A composite index `(a, b)` supports queries on `a` alone but NOT on `b` alone.

### Index overhead
- Every index adds write overhead (INSERT, UPDATE, DELETE must update the index).
- Watch for **index bloat** on write-heavy tables — schedule `REINDEX` or `VACUUM` as needed.
- Remove unused indexes: query `pg_stat_user_indexes` (PostgreSQL) to find zero-scan indexes.

### Index types
- **B-tree** (default): equality and range queries. Use for most cases.
- **GIN**: full-text search, JSONB containment (`@>`), array overlap.
- **GiST**: geometric data, range types.
- **Partial index**: `CREATE INDEX ... WHERE condition` — smaller, faster for selective queries.

## Migration best practices

### Always reversible
- Every migration must have both an `up` (apply) and `down` (revert) function.
- The `down` must actually restore the previous state — not just `DROP TABLE`.

### Column lifecycle
- **Adding a nullable column:** safe to apply in production with no downtime.
- **Adding a NOT NULL column:** add as nullable first → backfill → add constraint. Never add NOT NULL without a default in a single migration on a large table.
- **Renaming a column:** three-phase migration: (1) add new column + sync writes, (2) backfill + update reads, (3) remove old column. Never rename in one step on a live table.
- **Dropping a column:** only after all code references are removed and deployed. Never drop in the same migration that removes the code.

### Large table migrations
- Adding an index on a large table: use `CREATE INDEX CONCURRENTLY` (PostgreSQL) to avoid locking.
- Backfilling large tables: batch the updates — `UPDATE ... WHERE id BETWEEN ? AND ?` — to avoid long-running transactions and lock contention.
- Test migration timing on a production-sized database snapshot before running in production.

### Migration hygiene
- One logical change per migration — do not combine unrelated changes.
- Name migrations descriptively: `20240315_add_soft_delete_to_users`, not `migration_003`.
- Never edit a migration that has been applied to production — write a new one.

## Query patterns

### Avoiding N+1
- Use **joins** or **eager loading** instead of querying in a loop.
- Example: instead of loading 100 users then querying each user's posts separately (100+1 queries), use a single join or a batch query (`WHERE user_id IN (?)`).
- ORM-specific: use `include`/`with` in Prisma, `eager_load` in ActiveRecord, `joinedload` in SQLAlchemy.

### Query analysis
- Run `EXPLAIN ANALYZE` on slow queries before optimizing — verify the actual query plan.
- Look for: sequential scans on large tables, nested loop joins on large datasets, incorrect row estimates.
- Add indexes only after `EXPLAIN` confirms they are needed.

### Connection pooling
- Use a connection pooler (PgBouncer, or the pooler built into Prisma/Drizzle) — never open a new connection per request.
- In serverless environments, use transaction-mode pooling; in long-running servers, use session-mode.
- Set reasonable pool sizes — `max_connections` in Postgres has a hard limit; too many connections degrades performance.
