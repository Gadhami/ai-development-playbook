# Project Overview

<!-- CONFIGURE: Replace with your project details -->
- **Project:** [Your Project Name]
- **Repository:** [GitHub URL]
- **Stack:** Angular, TypeScript, SCSS
- **State Management:** [NgRx / Signals / Service-based]
- **Testing:** Jasmine + Karma (or Jest)

## Core Principles

You are a senior software engineer. Write production-grade code.

- **SOLID** — Single responsibility. Open/closed. Liskov substitution. Interface segregation. Dependency inversion.
- **DRY** — Extract when a pattern appears 3+ times. Not before.
- **YAGNI** — Don't build what isn't needed yet.
- **KISS** — Simplest correct solution wins.
- Favor composition over inheritance.
- Fail fast. Validate at system boundaries. Trust internal code.
- Make illegal states unrepresentable through the type system.

## Code Quality

- Comments explain **why**, never **what**.
- No dead code, no unused imports, no TODO without a ticket reference.
- Prefer immutable data: `readonly`, `const`, `Object.freeze()`.
- Return early to reduce nesting.
- Explicit over implicit. No `any` unless documented with a reason.

## Angular Architecture

### Component Design
- **Standalone components** for all new code. No NgModules.
- **Smart/Dumb pattern:** Container components handle data and state; presentational components are pure (inputs/outputs only).
- **OnPush** change detection for all components.
- **Signals** for component state. `computed()` for derived values. `effect()` for side effects.
- **`inject()`** function over constructor injection.

### State Management
- Signals for local component state.
- Services with signals for shared state across related components.
- NgRx or signal-based stores for complex cross-feature state (if applicable).

### Routing & Lazy Loading
- Route-level lazy loading for all feature modules.
- `@defer` blocks for non-critical UI sections.
- Route guards as functional guards (`CanActivateFn`).

### Forms
- Reactive forms over template-driven forms.
- Typed forms (`FormGroup<T>`, `FormControl<T>`).
- Validation in the form definition, not the template.

### HTTP & Data
- All HTTP calls through dedicated services. Never in components.
- Typed request/response interfaces.
- Error handling with interceptors for cross-cutting concerns.
- Loading and error states for all async operations.

## Working Style

- Read existing code before modifying. Match the patterns already in use.
- One concern per change. Small, focused modifications.
- Ensure the application compiles without errors before completing work.
- Run affected tests. All must pass.
- Accessibility matters: semantic HTML, ARIA labels, keyboard navigation.
