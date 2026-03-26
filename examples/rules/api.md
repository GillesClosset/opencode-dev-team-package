# API Conventions

> **When to use:** Load this rule file at the start of any session working
> on the API layer (routes, controllers, middleware).
>
> **WISC layer:** L2 — On-demand rules. This file should NOT be in AGENTS.md
> (that's L1, always-loaded). It loads only when the work enters this zone.

## Framework

- {Framework and version, e.g. Express 5, Hono 4, FastAPI 0.110}
- {API style, e.g. REST, GraphQL, tRPC}
- {Auth approach, e.g. JWT, session-based, API keys}

## Route Patterns

{Describe how routes are organized. Examples: file-based routing,
centralized router, controller classes. Note any naming conventions
for endpoints.}

```
{Example route structure:
GET    /api/{resource}        — List
POST   /api/{resource}        — Create
GET    /api/{resource}/:id    — Get by ID
PUT    /api/{resource}/:id    — Update
DELETE /api/{resource}/:id    — Delete
}
```

## Request/Response Conventions

{Describe standard response shapes, error response format, pagination
patterns, and any envelope or wrapper conventions.}

```
{Example error response:
{ "error": "message", "code": "ERROR_CODE", "details": {} }
}
```

## Middleware

{List key middleware and their order. Examples: auth, CORS, rate limiting,
request logging, error handling.}

## Validation

{Describe how input validation is handled. Examples: Zod schemas,
class-validator, Joi, manual validation. Note where validation
happens (middleware, controller, service layer).}

## Key Files

- `{path/to/routes/}` — Route definitions
- `{path/to/middleware/}` — Middleware stack
- `{path/to/validators/}` — Request validation schemas
- `{path/to/errors}` — Error types and error handling

## Testing

{How to test API endpoints. Patterns for integration tests,
mock database setup, authentication in tests.}

## Anti-patterns

- {e.g. Never expose internal error details in production responses}
- {e.g. Never skip input validation — validate all user input}
- {e.g. Never call external services directly from route handlers — use a service layer}
- {e.g. Never log sensitive data (passwords, tokens, PII)}
