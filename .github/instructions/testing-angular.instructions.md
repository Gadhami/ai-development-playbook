---
description: 'Angular testing standards'
applyTo: '**/*.spec.ts'
---

# Angular Testing Standards

## Test or Skip?

**Test:** Components with logic or state, services with business rules, pipes with transformation logic, guards with conditions, interceptors, complex template interactions.

**Skip:** Components that are pure presentation with no logic, trivial services that only proxy HTTP calls, simple type definitions.

## Structure

- One `.spec.ts` per source file, colocated in the same directory.
- **Arrange–Act–Assert.** One behavior per test.
- Use `describe` blocks to group by method or behavior. Use `it` for individual cases.
- Descriptive test names: `it('should emit selected item when clicked')`.

## Component Testing

- Use `TestBed` for component tests that need Angular's DI and rendering.
- Prefer testing the component class directly for pure logic tests.
- Use `ComponentFixture` and `DebugElement` for template interaction tests.
- Always call `fixture.detectChanges()` after setup.
- When using signals, set input signals via `componentRef.setInput('name', value)`.

## Service Testing

- Instantiate services directly with mocked dependencies when possible.
- Use `HttpClientTestingModule` / `provideHttpClientTesting()` for HTTP service tests.
- Verify HTTP method, URL, and request body. Flush with expected response.
- Test error handling paths (`.error()` on the mock request).

## Mocking

- Use `jasmine.createSpyObj()` or Jest mocks for dependency injection.
- Provide mocks via `TestBed.configureTestingModule({ providers: [{ provide: Service, useValue: mockService }] })`.
- Mock only what crosses boundaries. Don't mock the unit under test.

## Async Testing

- Use `fakeAsync` + `tick()` for timer-based async.
- Use `waitForAsync` (or `async`/`await`) for promise/observable-based async.
- Always flush pending microtasks before assertions.

## Anti-Patterns to Avoid

- Testing Angular framework behavior (e.g., "does DI work?").
- Snapshot tests for frequently changing templates.
- Tests coupled to CSS selectors that break on style changes — prefer `data-testid` attributes.
- Over-mocking: if everything is mocked, the test proves nothing.
