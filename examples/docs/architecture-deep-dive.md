# Architecture Deep Dive

> **Purpose:** End-to-end flow traces across the system with file references.
> **When to use:** Understanding data flows, debugging cross-system issues, onboarding.
> **Size:** ~300 lines — use the scout agent to check relevance before loading.

---

## 1. Request Flow

{Trace a request from entry point through routing, business logic, data access,
and response. Include file:line references where possible.}

```
{Example flow:
Client request
  → Router ({path/to/router})
    → Middleware: auth, validation ({path/to/middleware})
      → Controller/Handler ({path/to/handler})
        → Service layer ({path/to/service})
          → Data access ({path/to/repository})
            → Database query
          ← Result
        ← Business logic applied
      ← Response formatted
    ← Middleware: logging, error handling
  ← Response sent
}
```

**Key decision points:**
- {Where routing decisions happen}
- {Where authentication/authorization is checked}
- {Where business logic vs. data access is separated}

---

## 2. Data Model

{Key entities, their relationships, and storage layer.}

| Entity | Table/Collection | Purpose |
|--------|-----------------|---------|
| {entity} | {table} | {purpose} |

{Describe key relationships: one-to-many, many-to-many, foreign keys.}

---

## 3. Configuration Loading

{How configuration is loaded, layered, and overridden.}

```
{Example:
Layer 1: Code defaults
Layer 2: Config file ({path})
Layer 3: Environment variables (highest precedence)
}
```

---

## 4. Cross-Cutting Patterns

{Logging, error handling, authentication, caching, and other patterns
that span multiple modules.}

### Logging
{Pattern description and example}

### Error Handling
{Pattern description and example}

### Authentication
{Pattern description and example}

---

## 5. Key File Reference

| Flow | Key Files |
|------|-----------|
| {flow} | `{path}`, `{path}` |
| {flow} | `{path}`, `{path}` |
