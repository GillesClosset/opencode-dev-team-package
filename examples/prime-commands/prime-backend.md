---
description: Prime agent with backend context before starting work
agent: scout
---

# Prime Backend: Server and API Orientation

## Objective

Orient on the backend before working on server logic, API routes, business
logic, or data access.

## Process

### 1. Understand the Backend Structure

```bash
ls {path/to/backend/src/}
```

### 2. Understand the Request Flow

Read the main server entry point to understand:
- How the server starts and configures itself
- How routes are registered
- How middleware is applied

Read the primary router/controller to understand:
- How requests are dispatched
- How authentication is handled
- How errors are returned

### 3. Understand the Data Layer

List data access modules:
```bash
ls {path/to/db/or/models/}
```

Read the database connection setup and key query modules to understand
the data access patterns.

### 4. Understand the Service Layer

Read key service/business logic modules to understand:
- How business rules are enforced
- How external services are called
- How errors are classified and propagated

### 5. Check Recent Backend Activity

```bash
git log -8 --oneline -- {path/to/backend/}
```

## Output

Summarize (under 250 words):

### Request Flow
- Entry point → routing → business logic → data access → response
- Key middleware and their order

### Data Layer
- Database type and access patterns
- Key tables/collections and their purpose

### Service Layer
- Key business logic modules
- External service integrations

### Recent Changes
- Last few backend commits
