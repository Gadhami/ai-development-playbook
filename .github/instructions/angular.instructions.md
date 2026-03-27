---
description: 'Angular & TypeScript coding standards'
applyTo: '**/*.ts'
---

# Angular & TypeScript Standards

## TypeScript

- Strict mode. No `any` unless unavoidable — document the reason with a comment.
- `const` by default; `let` only for reassignment; never `var`.
- Interfaces for object shapes. Type aliases for unions and intersections.
- `readonly` for properties that must not change after initialization.
- Destructure objects and arrays when it improves readability.
- Prefer `unknown` over `any` when the type is genuinely dynamic.
- Use `satisfies` operator to validate types without widening.

## Naming

- `PascalCase` for classes, interfaces, types, enums, decorators.
- `camelCase` for variables, functions, methods, properties.
- `UPPER_SNAKE_CASE` for true constants.
- Files: `feature-name.component.ts`, `feature-name.service.ts`, `feature-name.model.ts`.
- Interfaces: no `I` prefix (TypeScript convention). Use descriptive names: `User`, `OrderSummary`.

## Angular Components

- Standalone components only. No `NgModule` for new code.
- `inject()` function over constructor injection.
- `OnPush` change detection on every component.
- Signals for component state. `computed()` for derived state. `effect()` sparingly for side effects.
- Smart/Dumb pattern: container components fetch data; presentational components receive via `input()` and emit via `output()`.
- Signal-based inputs: `input()`, `input.required()`. Signal-based outputs: `output()`.

## Templates

- `@if`, `@for`, `@switch` control flow (new built-in syntax over `*ngIf`, `*ngFor`).
- `trackBy` via `track` expression on all `@for` loops.
- `@defer` for lazily loaded non-critical content.
- No complex expressions in templates. Extract to `computed()` signals or pipes.

## Reactive Patterns

- Prefer signals over observables for synchronous state.
- Observables for inherently async streams: HTTP, WebSockets, DOM events.
- Always unsubscribe: `takeUntilDestroyed()`, `async` pipe, or `DestroyRef`.
- Declarative streams (`switchMap`, `combineLatest`, `merge`) over imperative `.subscribe()` chains.
- `toSignal()` to bridge observables into signal-based components.

## Forms

- Reactive forms with typed controls (`FormControl<string>`, `FormGroup<T>`).
- Validation logic in the form definition, not in the template.
- Custom validators as pure functions.

## HTTP & Data

- All HTTP calls in dedicated services. Never directly in components.
- Typed request/response interfaces. No `any` responses.
- Handle loading, error, and empty states for every async operation.
- Use interceptors for cross-cutting concerns (auth headers, error handling, retry).

## Performance

- `track` expressions for all `@for` loops.
- Lazy load routes. `@defer` non-critical UI.
- Avoid expensive computation in templates — use `computed()` or pure pipes.
- `provideHttpClient(withInterceptors([...]))` for tree-shakeable HTTP setup.

## Accessibility

- Semantic HTML elements (`<nav>`, `<main>`, `<button>`, `<article>`).
- ARIA attributes where native semantics are insufficient.
- All interactive elements must be keyboard accessible.
- Meaningful `alt` text for images. `aria-label` for icon-only buttons.
