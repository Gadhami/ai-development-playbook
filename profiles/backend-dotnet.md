# Project Overview

<!-- CONFIGURE: Replace with your project details -->
- **Project:** [Your Project Name]
- **Repository:** [GitHub URL]
- **Stack:** .NET, C#, SQL Server, Entity Framework Core
- **Architecture:** Clean Architecture
- **Testing:** xUnit (or MSTest/NUnit) + FluentAssertions + Moq

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

- Least-exposure: `private` > `internal` > `protected` > `public`.
- Comments explain **why**, never **what**.
- No dead code, no unused parameters, no TODO without a ticket reference.
- Prefer immutable data: records, readonly, init-only, const.
- Return early to reduce nesting.
- Explicit over implicit. No magic strings. No stringly-typed code.

## Clean Architecture

When creating new features, follow Clean Architecture layers:

| Layer | Depends On | Contains |
|---|---|---|
| **Domain** | Nothing | Entities, Value Objects, Domain Events, Enums, Domain Services |
| **Application** | Domain | Use Cases (Commands/Queries via CQRS), DTOs, Interfaces, Validation |
| **Infrastructure** | Application, Domain | EF Core, Repositories, External Services, File I/O |
| **Presentation** | Application | Controllers, API Endpoints, Middleware |

**Dependency rule:** Dependencies flow inward only. Domain has zero outward dependencies.

### Domain Layer
- Entities encapsulate behavior. No anemic domain models.
- Value objects for identity-less concepts (Money, Email, DateRange).
- Domain events for cross-aggregate side effects.
- No framework dependencies. No EF Core attributes. No data annotations.

### Application Layer
- CQRS: Commands mutate state, Queries read state. Separate handlers.
- MediatR for dispatching. Pipeline behaviors for cross-cutting concerns.
- FluentValidation for input validation.
- Define interfaces here; implement in Infrastructure.
- DTOs at layer boundaries. Never expose domain entities to outer layers.

### Infrastructure Layer
- Implements Application-defined interfaces.
- EF Core configurations via `IEntityTypeConfiguration<T>`, not attributes.
- Configuration via `IOptions<T>`.
- Register all services via `AddInfrastructureServices()` extension method.

## Working Style

- Read existing code before modifying. Match the patterns already in use.
- One concern per change. Small, focused modifications.
- Always ensure the solution builds before completing work.
- Run tests affected by your changes. All must pass.
- When implementing a feature, consider: What tests prove this works? Write them.
