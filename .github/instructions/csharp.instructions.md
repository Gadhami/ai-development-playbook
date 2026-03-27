---
description: 'C# coding standards'
applyTo: '**/*.cs'
---

# C# Standards

## Modern C#

- File-scoped namespaces.
- Records for DTOs, value objects, and immutable data.
- Pattern matching: switch expressions, `is`, `and`/`or` patterns, list patterns.
- Target-typed `new()` when type is obvious from context.
- Raw string literals (`"""`) for multi-line strings.
- `required` modifier for mandatory properties on models.
- Collection expressions (`[1, 2, 3]`) when supported by TFM.
- Primary constructors for simple DI scenarios when supported.

## Naming

- `PascalCase` for types, methods, properties, events, constants.
- `camelCase` for parameters and local variables.
- `_camelCase` for private fields.
- `I` prefix for interfaces. No `Dto` suffix on domain models; use `Dto` suffix only for data transfer objects crossing layer boundaries.
- Async methods end with `Async`.

## Async

- Accept and forward `CancellationToken` end-to-end.
- No fire-and-forget. No sync-over-async (`Task.Result`, `.Wait()`, `.GetAwaiter().GetResult()`).
- `ConfigureAwait(false)` in library code only.
- Default to `Task<T>`; use `ValueTask<T>` only when measured to help.

## Error Handling

- `ArgumentNullException.ThrowIfNull(x)` for null guards. `ArgumentException.ThrowIfNullOrWhiteSpace(x)` for strings.
- Precise exception types: `ArgumentException`, `InvalidOperationException`, `NotFoundException`.
- Never catch base `Exception` without rethrowing. Never swallow exceptions silently.
- Structured logging: `_logger.LogWarning("Order {OrderId} not found", orderId)` — no string interpolation in log calls.

## Collections

- Return `IReadOnlyList<T>` or `IReadOnlyCollection<T>` from methods.
- Accept `IEnumerable<T>` or `IReadOnlyList<T>` as parameters.
- LINQ `Select`/`Where` over manual loops for transformations and filtering.
- `IAsyncEnumerable<T>` for streaming large datasets.

## EF Core

- `TagWithCallSite()` as the first call on all queries for diagnostics.
- `AsNoTracking()` for read-only queries.
- Prefer batch/bulk APIs over sequential `await` in loops.
- Queries project directly to DTOs via `Select()`. No querying full entities when only a subset of fields is needed.
- Configurations via `IEntityTypeConfiguration<T>`, not data annotations.

## Performance

- Simple first; optimize hot paths only when measured.
- `Span<T>` / `Memory<T>` / `ArrayPool` for hot paths with heavy allocations.
- Stream large payloads; avoid buffering entire responses in memory.
- Async end-to-end. No blocking calls on async code paths.

## Security

- Never log secrets, tokens, passwords, or PII.
- Validate and sanitize all external input at system boundaries.
- Use parameterized queries (EF Core handles this). Never concatenate SQL.
- `IOptions<T>` for configuration. No hardcoded connection strings or secrets.
