# Project Overview

<!-- CONFIGURE: Replace with your project details -->
- **Project:** [Your Project Name]
- **Repository:** [GitHub URL]
- **Backend:** .NET, C#, SQL Server, Entity Framework Core
- **Frontend:** Angular, TypeScript, SCSS
- **Architecture:** Clean Architecture (Backend), Component-based (Frontend)
- **Testing:** xUnit + FluentAssertions + Moq (Backend), Jasmine + Karma or Jest (Frontend)

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

- Least-exposure: prefer the most restrictive access modifier that works.
- Comments explain **why**, never **what**.
- No dead code, no unused parameters/imports, no TODO without a ticket reference.
- Prefer immutable data: records, readonly, const, init-only setters.
- Return early to reduce nesting.
- Explicit over implicit. No magic strings. No `any` unless documented.

## Backend — Clean Architecture

When creating new backend features, follow Clean Architecture layers:

| Layer | Depends On | Contains |
|---|---|---|
| **Domain** | Nothing | Entities, Value Objects, Domain Events, Enums, Domain Services |
| **Application** | Domain | Use Cases (Commands/Queries via CQRS), DTOs, Interfaces, Validation |
| **Infrastructure** | Application, Domain | EF Core, Repositories, External Services, File I/O |
| **Web/API** | Application | Controllers, API Endpoints, Middleware |

**Dependency rule:** Dependencies flow inward only. Domain is the core with zero outward dependencies.

- Entities encapsulate behavior. No anemic domain models.
- CQRS: Commands mutate, Queries read. MediatR for dispatching.
- FluentValidation for input validation via pipeline behaviors.
- Define interfaces in Application; implement in Infrastructure.
- DTOs at layer boundaries. Never expose domain entities to API consumers.
- EF Core configurations via `IEntityTypeConfiguration<T>`, not attributes.

## Frontend — Angular Architecture

- **Standalone components** for all new code. No NgModules.
- **Smart/Dumb pattern:** Containers handle data; presentational components are pure.
- **OnPush** change detection. **Signals** for state. `computed()` for derived values.
- **`inject()`** function over constructor injection.
- Route-level lazy loading. `@defer` for non-critical UI.
- Reactive forms with typed controls. Validation in form definition.
- HTTP calls in services only, never in components.

## API Contract

- Backend exposes RESTful endpoints. Frontend consumes via typed HTTP services.
- Shared models: keep API DTOs in sync between backend response types and frontend interfaces.
- Always handle loading, error, and empty states in the UI for every API call.

## Working Style

- Read existing code before modifying. Match the patterns already in use.
- One concern per change. Small, focused modifications.
- Ensure both backend and frontend compile without errors before completing work.
- Run affected tests on both sides. All must pass.
- When implementing a feature end-to-end, consider: API contract first, then backend, then frontend.
