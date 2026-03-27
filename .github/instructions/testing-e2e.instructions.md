---
description: 'E2E / integration testing standards for Angular'
applyTo: '**/*.e2e-spec.ts,**/e2e/**/*.ts,**/playwright/**/*.ts,**/cypress/**/*.ts'
---

# E2E Testing Standards

## Framework Choice

- **Playwright** (recommended) or **Cypress** for new projects.
- Protractor is deprecated. Migrate off it if encountered.

## Test Design

- Test **user workflows**, not implementation details. Think: "what does the user see and do?"
- Each test should be independent. No test should depend on the outcome of another.
- Use `data-testid` attributes for selectors. Never rely on CSS classes, element structure, or text content that may change.
- One assertion focus per test. Test a single user flow or outcome.

## Page Object Model

- Encapsulate page interactions in Page Object classes.
- Page objects expose **actions** (`login(user, pass)`) and **queries** (`getErrorMessage()`), not raw selectors.
- Keep selectors private inside page objects.
- Reuse page objects across tests.

```typescript
// pages/login.page.ts
export class LoginPage {
  private readonly emailInput = '[data-testid="email-input"]';
  private readonly submitButton = '[data-testid="submit-button"]';
  private readonly errorMessage = '[data-testid="error-message"]';

  async login(email: string, password: string) { /* ... */ }
  async getError(): Promise<string> { /* ... */ }
}
```

## API & Data Setup

- **Seed test data via API calls or database scripts**, not through the UI.
- Reset state before each test (or test suite), not after.
- Never share mutable state between tests.
- Use factories or builders for test data.

## Waiting & Stability

- Never use `sleep()` or fixed timeouts.
- Wait for specific conditions: element visible, network request complete, text appears.
- Use Playwright's auto-waiting or Cypress's built-in retry-ability.
- For SPAs: wait for the router to settle before asserting.

## Network

- Mock external APIs when testing UI behavior. Use real APIs for integration tests.
- Intercept network requests to assert payloads and test error scenarios.
- Test loading states, error states, and empty states — not just the happy path.

## CI Considerations

- E2E tests run in headless mode in CI.
- Capture screenshots and traces on failure automatically.
- Keep E2E suites fast: parallelize where possible, test critical paths only.
- Flaky tests are worse than no tests. Fix or quarantine immediately.

## Anti-Patterns

- Tests that break when CSS or layout changes.
- Tests that require a specific execution order.
- Asserting on exact pixel positions or animation states.
- Over-testing: E2E for logic that should be a unit test.
