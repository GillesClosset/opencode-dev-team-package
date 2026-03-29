---
name: webapp-testing
description: Use this skill when validating a local web app in a browser with Playwright-style workflows, assertions, and debugging artifacts.
---

# Webapp Testing — Browser Validation with Playwright

## When to activate

Load this skill when:
- Verifying a local web app or frontend flow in a real browser
- Writing or reviewing Playwright-based end-to-end checks
- Confirming a user-visible fix actually works beyond unit tests
- Debugging flaky browser interactions, loading states, or rendered output
- Defining browser-level acceptance checks for a UI-heavy lot

Do **not** use this skill for general unit-test conventions, API-only testing, or repository-specific helper scripts that are not already present.

## Preconditions

- Confirm the project already has a usable Playwright setup or explicit instructions for starting the app under test.
- Use project-local commands and configuration; do not invent wrapper scripts, server orchestration helpers, or screenshot utilities.
- If Playwright is not installed in the target project, stop and report that dependency gap instead of guessing setup steps.

## Key questions before testing

1. What exact user path or acceptance criterion needs browser validation?
2. Which local command starts the app, and how will you know it is ready?
3. Which visible states prove success: heading text, button enabled, toast shown, URL changed, data rendered?
4. What existing selectors are stable and user-facing?
5. What artifacts will help if the check fails: screenshot, trace, console output, network errors?

## Recommended workflow

### 1. Recon first
- Identify the page or flow to validate.
- Read existing Playwright config or tests before adding new ones.
- Reuse existing test structure, fixtures, and naming patterns.
- Prefer the smallest browser scenario that proves the lot works.

### 2. Use resilient locators
- Prefer user-facing locators such as `getByRole`, `getByLabel`, `getByText`, and `getByPlaceholder`.
- Use `data-testid` only when semantic or visible selectors are genuinely insufficient.
- Avoid brittle CSS chains, nth-child selectors, and selectors tied to implementation-only DOM structure.

```ts
await page.getByLabel('Email').fill('person@example.com')
await page.getByRole('button', { name: 'Sign in' }).click()
await expect(page.getByText('Welcome back')).toBeVisible()
```

### 3. Assert rendered outcomes, not internal guesses
- Prefer web-first assertions such as `toBeVisible`, `toHaveText`, `toHaveURL`, and `toBeEnabled`.
- Assert what the user can observe after the action completes.
- Let Playwright retry until the expected state is true instead of manually polling.

```ts
await expect(page.getByRole('heading', { name: 'Dashboard' })).toBeVisible()
await expect(page.getByText('Submitted')).toBeVisible()
```

### 4. Let Playwright wait for you
- Playwright actions auto-wait for actionability.
- Web-first assertions auto-retry until timeout.
- Prefer waiting on specific UI state over sleeping for time.
- Avoid `page.waitForTimeout()` for production checks; current Playwright docs explicitly discourage hard waits because they are flaky.

Prefer patterns like:
- `await expect(locator).toBeVisible()`
- `await expect(locator).toHaveText(...)`
- `await expect(page).toHaveURL(...)`
- waiting for a loading indicator to disappear only when that indicator is part of the product behavior

### 5. Keep tests isolated
- Treat each test as independent.
- Reuse Playwright's per-test `page` and `context` fixtures when available.
- Do not depend on state leaked from a previous test.
- Seed or navigate into the needed state explicitly inside the test.

## Debugging workflow

- Capture a screenshot when a user-visible state is wrong or surprising.
- Use Playwright trace artifacts for multi-step failures.
- Review browser console errors and failed network requests when the UI does not settle.
- Preserve the failing selector and assertion in the report so another agent can reproduce it.

Trace example:

```ts
await context.tracing.start({ screenshots: true, snapshots: true })
// ... run scenario ...
await context.tracing.stop({ path: 'trace.zip' })
```

## Safe usage guardrails

- Do not run destructive flows against shared or production environments unless explicitly authorized.
- Prefer local dev servers, seeded test accounts, and reversible data.
- Keep screenshots and traces scoped to the requested lot; avoid collecting unrelated sensitive data.
- If the browser workflow depends on credentials or services you do not have, report the blocker clearly instead of simulating success.

## Review checklist

- [ ] The flow under test matches a stated acceptance criterion.
- [ ] Selectors are user-facing and stable.
- [ ] Assertions describe visible end state, not arbitrary delays.
- [ ] No blind sleeps were added where a locator assertion would work.
- [ ] The test is isolated from other tests.
- [ ] Failure artifacts are available or the report explains how to reproduce them.
- [ ] The skill guidance does not assume helper scripts that this repo or target project does not ship.
