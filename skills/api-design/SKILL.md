---
name: api-design
description: Use this skill when designing new endpoints, reviewing API contracts, making versioning decisions, or standardizing error responses — covers REST, GraphQL, and tRPC conventions.
---

# API Design — REST, GraphQL, and tRPC Conventions

## When to activate

Load this skill when:
- Designing new REST endpoints or reviewing existing ones
- Choosing between REST, GraphQL, and tRPC for a new interface
- Standardizing error response format across an API
- Making API versioning decisions
- Reviewing a GraphQL schema or resolver design
- Setting up tRPC procedures and input validation

## Key questions before designing

1. Is this a public API (external consumers) or internal (service-to-service)?
2. Does it need versioning now, or can that be deferred?
3. What is the pagination strategy (cursor vs offset)?
4. How will errors be surfaced to the client?
5. Are any mutations non-idempotent? Do they need idempotency keys?

## REST conventions

### Resource naming
- Use **nouns**, not verbs: `/users`, `/orders`, `/documents` — not `/getUser`, `/createOrder`.
- Use plural nouns for collections: `/users` (list), `/users/{id}` (single resource).
- Nest resources only when the child cannot exist without the parent: `/users/{id}/posts`.
- Keep nesting shallow — avoid `/a/{id}/b/{id}/c/{id}` patterns.

### HTTP verb semantics
| Verb | Use | Idempotent? |
|------|-----|-------------|
| GET | Read — never modifies state | Yes |
| POST | Create or non-idempotent action | No |
| PUT | Replace (full resource update) | Yes |
| PATCH | Partial update | No (unless designed carefully) |
| DELETE | Remove | Yes |

### Status codes
| Code | Meaning |
|------|---------|
| 200 | OK — general success |
| 201 | Created — resource was created (include `Location` header) |
| 204 | No Content — success with no response body (DELETE, some PUT) |
| 400 | Bad Request — client error, invalid input |
| 401 | Unauthorized — not authenticated |
| 403 | Forbidden — authenticated but not authorized |
| 404 | Not Found |
| 409 | Conflict — e.g. duplicate resource |
| 422 | Unprocessable Entity — syntactically valid but semantically invalid input |
| 429 | Too Many Requests — rate limited |
| 500 | Internal Server Error |

### Error response format
Always return structured errors — **never expose stack traces in production**:
```json
{
  "error": {
    "code": "VALIDATION_FAILED",
    "message": "Email address is invalid",
    "details": [
      { "field": "email", "message": "Must be a valid email address" }
    ]
  }
}
```
The `details` field is optional but useful for validation errors.

### Pagination
- **Cursor-based** (preferred for real-time data): `{ cursor, limit }` request, `{ data, nextCursor }` response. Stable across inserts.
- **Offset-based** (simpler, acceptable for static/slow-changing data): `{ page, pageSize }` request, `{ data, total, page, pageSize }` response.
- Always include a maximum page size to prevent unbounded queries.

### Idempotency
For mutations that create or modify state and may be retried (payments, order creation):
- Accept an `Idempotency-Key` header.
- Cache the response for duplicate requests with the same key.

### Versioning
- **Public APIs:** URL versioning — `/v1/`, `/v2/`. Explicit, easy to route, easy to document.
- **Internal APIs:** Header versioning (`API-Version: 2024-01-01`) or omit versioning if consumers are co-deployed.
- Maintain at least one previous version before deprecation. Announce deprecation with a `Deprecation` response header.

## GraphQL conventions

### Operations
- `query` — read-only, cacheable.
- `mutation` — state-changing, not cached.
- `subscription` — real-time event stream.

### N+1 problem
- Use DataLoader (or equivalent) to batch and cache per-field resolver calls.
- Never make a database query inside a resolver loop without batching.

### Error handling in resolvers
- Return `null` with errors in the `errors` array for field-level failures.
- Use union types (`Result | Error`) for mutations where partial success is possible.
- Never throw unhandled exceptions in resolvers — catch and format them.

### Schema design
- **Schema-first** (write SDL, generate types) is preferred for team alignment.
- **Code-first** (Nexus, Pothos) is acceptable when strong type safety is the priority.
- Keep mutation inputs as separate input types — do not reuse output types as inputs.

## tRPC conventions

### Procedure naming
- Use camelCase: `user.getById`, `post.create`, `auth.refreshToken`.
- Group related procedures into routers (e.g., `userRouter`, `postRouter`).

### Input validation
- Always validate inputs with Zod — do not trust client-supplied data.
- Use `.transform()` for normalization (e.g., trim whitespace, lowercase email).
- Validate enums explicitly: `z.enum(["active", "inactive"])` not `z.string()`.

### Error types
- Use `TRPCError` with appropriate `code` values: `BAD_REQUEST`, `UNAUTHORIZED`, `FORBIDDEN`, `NOT_FOUND`, `INTERNAL_SERVER_ERROR`.
- Include a human-readable `message` — it surfaces in client error handling.
