# Code Review Instructions

Review pull requests for the following concerns, in priority order.

## 1. Correctness

- Logic errors, off-by-one mistakes, unhandled edge cases.
- Async/await misuse: fire-and-forget, missing cancellation token forwarding, sync-over-async.
- Null reference risks where nullable context is enabled.

## 2. Security

- SQL injection, XSS, command injection vectors.
- Secrets, credentials, or PII in code or comments.
- Missing authorization checks on endpoints or service methods.
- User input that reaches a database, file system, or external service without validation or sanitization.

## 3. Architecture

- Dependency direction violations (outer layers referenced by inner layers).
- Business logic in controllers, middleware, or presentation layer.
- Domain entities exposed directly as API responses (should use DTOs).
- Concrete dependencies where an interface should be injected.

## 4. Performance

- N+1 query patterns in EF Core or similar ORMs.
- Sequential `await` in loops for independent operations (should use bulk/batch or parallel).
- Missing `AsNoTracking()` on read-only EF Core queries.
- Unbounded queries without pagination or `Take()` limits.
- Large object allocations in hot paths.

## 5. Testing

- New behavior without corresponding tests.
- Tests that only verify mock interactions without checking outputs or side effects.
- Tests coupled to implementation details rather than observable behavior.

## 6. Code Quality

- Dead code, unused parameters, commented-out code.
- Overly broad access modifiers (public when internal or private would suffice).
- Magic strings or numbers without named constants.
- Duplicated logic that should be extracted.

## Severity Guide

- **Blocking:** Bugs, security vulnerabilities, architectural violations, breaking changes without migration path.
- **Advisory:** Style improvements, minor performance suggestions, test coverage gaps for non-critical paths.
