---
description: Prime agent with frontend context before starting work
agent: scout
---

# Prime Frontend: UI Orientation

## Objective

Orient on the frontend before working on UI components, pages, data
fetching, or styling.

## Process

### 1. Understand the Frontend Stack

Read `package.json` (or equivalent) for exact dependency versions and
build scripts.

### 2. Understand the Route and Layout Structure

Read the main application entry point to understand:
- Route definitions and URL structure
- Layout components and nesting
- Error boundaries and fallbacks

List route pages:
```bash
ls {path/to/pages/or/routes/}
```

List component directories:
```bash
ls {path/to/components/}
```

### 3. Understand Component Organization

Explore the component directory structure to understand:
- How components are grouped (by feature, by type, by page)
- Naming conventions
- Shared vs. page-specific components

### 4. Understand Data Fetching

Read the API client and data fetching hooks to understand:
- How REST/GraphQL calls are made
- Caching and invalidation patterns
- Real-time data patterns (SSE, WebSocket, polling)

### 5. Understand Styling

Read the theme/styling configuration to understand:
- Design tokens and CSS variables
- Component library usage
- Responsive design patterns

### 6. Check Recent Frontend Activity

```bash
git log -8 --oneline -- {path/to/frontend/}
```

## Output

Summarize (under 200 words):

### Route Structure
- List each route and its URL path
- Note layout and navigation components

### Component Organization
- Key component groups and their responsibilities

### Data Fetching
- API client patterns
- Caching and real-time data approach

### Styling
- Design system and theme approach
- Component library and conventions

### Recent Changes
- Last few frontend commits
