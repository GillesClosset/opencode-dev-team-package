# Frontend Conventions

> **When to use:** Load this rule file at the start of any session working
> on the frontend / UI layer.
>
> **WISC layer:** L2 — On-demand rules. This file should NOT be in AGENTS.md
> (that's L1, always-loaded). It loads only when the work enters this zone.

## Tech Stack

- {Framework and version, e.g. React 19, Vue 3, Svelte 5}
- {Build tool, e.g. Vite, Webpack, Turbopack}
- {Styling approach, e.g. Tailwind CSS, CSS Modules, styled-components}
- {State management, e.g. TanStack Query, Zustand, Pinia}
- {Router, e.g. React Router, Vue Router, SvelteKit routing}

## Component Organization

{Describe the component directory structure and naming conventions.
Examples: feature-based folders, atomic design, barrel exports.}

## Data Fetching

{Describe the data fetching patterns used in the project.
Examples: query hooks, API client functions, SSE/WebSocket handlers.
Note any caching, polling, or invalidation conventions.}

## Styling Conventions

{Describe the styling approach and any design tokens or theme system.
Examples: CSS variable naming, utility class conventions, component
library usage patterns.}

## Routing

{Describe the routing structure, route naming conventions, and any
guards or middleware. List key routes if helpful.}

## Key Files

- `{path/to/app-entry}` — Application entry point and route setup
- `{path/to/api-client}` — API client and data fetching utilities
- `{path/to/components/}` — Shared component library
- `{path/to/styles}` — Theme and design token definitions

## Testing

{How to run frontend tests. Common patterns for component testing,
hook testing, and integration testing. Note any test utilities or
custom render wrappers.}

## Anti-patterns

- {e.g. Never import backend modules directly from frontend code}
- {e.g. Never use inline styles for theme colors — use design tokens}
- {e.g. Never open duplicate data connections — reuse the existing hook}
- {e.g. Never skip accessibility attributes on interactive elements}
