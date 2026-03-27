---
description: 'Review code for quality, security, architecture, and patterns'
---

# Code Review

Perform a thorough review of the selected code or recent changes.

## Review Checklist

### Correctness
- [ ] Logic is correct. Edge cases are handled.
- [ ] Error handling is appropriate (no swallowed exceptions, correct exception types).
- [ ] Async/await is used correctly (no fire-and-forget, cancellation tokens forwarded).

### Architecture
- [ ] Follows the project's architecture (layer boundaries, dependency direction).
- [ ] No business logic in controllers/presentation layer.
- [ ] Dependencies are injected, not constructed internally.
- [ ] Interfaces defined in the correct layer.

### Code Quality
- [ ] SOLID principles followed.
- [ ] No code duplication.
- [ ] Naming is clear and consistent.
- [ ] Methods are focused (single responsibility).
- [ ] Access modifiers are as restrictive as possible.

### Security
- [ ] User input is validated and sanitized.
- [ ] No SQL injection vectors (parameterized queries used).
- [ ] No secrets or credentials in code.
- [ ] Authorization checks are in place.

### Performance
- [ ] No N+1 query patterns.
- [ ] No unnecessary allocations in hot paths.
- [ ] `AsNoTracking()` on read-only EF Core queries.
- [ ] No sequential `await` in loops for independent operations.

### Testing
- [ ] New behavior has tests.
- [ ] Tests verify behavior, not implementation details.
- [ ] Test names describe the scenario and expected outcome.

## Output

For each finding, provide:
1. **Severity:** BLOCKING or ADVISORY
2. **Location:** File and line
3. **Issue:** What's wrong
4. **Fix:** How to fix it

End with an overall assessment: PASS, PASS WITH FOLLOW-UPS, or BLOCKED.
