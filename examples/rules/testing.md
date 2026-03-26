# Testing Conventions

> **When to use:** Load this rule file at the start of any session that
> involves writing or modifying tests.
>
> **WISC layer:** L2 — On-demand rules. This file should NOT be in AGENTS.md
> (that's L1, always-loaded). It loads only when the work enters this zone.

## Test Framework

- {Framework, e.g. Jest, Vitest, pytest, Go testing}
- {Assertion library, if separate from framework}
- {Mocking library, e.g. built-in mocks, testdouble, unittest.mock}

## Test Structure

{Describe the test file organization. Examples: co-located with source,
separate `__tests__/` directories, `tests/` at project root. Note
naming conventions for test files.}

```
{Example test structure:
describe('ComponentName', () => {
  beforeEach(() => {
    // Setup
  });

  test('does thing when condition', async () => {
    // Arrange
    // Act
    // Assert
  });
});
}
```

## Mocking Patterns

{Describe the project's mocking conventions. What gets mocked (external
services, databases, file system) vs. what uses real implementations.
Note any mock setup or teardown requirements.}

```
{Example mock pattern:
// Preferred: spy (restorable)
const spy = spyOn(module, 'method');
spy.mockImplementation(() => value);
afterEach(() => spy.mockRestore());

// External deps: module mock (use with caution)
mock.module('external-lib', () => ({ fn: mock(() => value) }));
}
```

## Test Isolation

{Describe how tests are isolated from each other. Note any process-level
mock pollution issues, test batching requirements, or parallel execution
constraints.}

## What to Test

- **Unit tests:** Individual functions, components, and modules in isolation
- **Integration tests:** Module boundaries, API endpoints, data flows
- **Edge cases:** Error handling, empty inputs, boundary values, timeouts
- **Regressions:** When fixing a bug, add a test that would have caught it

## Test Commands

```bash
# Run all tests
{test command}

# Run tests for a specific area
{targeted test command}

# Run tests in watch mode
{watch command}
```

## Key Files

- `{path/to/test-utils}` — Shared test utilities, fixtures, and helpers
- `{path/to/mocks/}` — Shared mock implementations
- `{path/to/fixtures/}` — Test data and fixtures

## Anti-patterns

- {e.g. Never test implementation details — test behavior and outputs}
- {e.g. Never use real external services in unit tests — always mock}
- {e.g. Never share mutable state between tests without cleanup}
- {e.g. Never skip error path testing — test what happens when things fail}
- {e.g. Never write tests that depend on execution order}
