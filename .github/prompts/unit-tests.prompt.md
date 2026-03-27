---
description: 'Generate high-quality unit tests for the selected code'
---

# Unit Test Generation

Analyze the selected code and generate comprehensive unit tests.

## Step 1: Evaluate Testability

Determine if the code is worth testing:
- **Worth testing:** Business logic, algorithms, validation, conditional behavior, data transformation, orchestration.
- **Not worth testing:** DTOs, constructors that only assign fields, single-line pass-throughs, trivial LINQ projections.

If the code isn't worth testing, explain why and stop.

## Step 2: Assess Dependencies

Identify:
- Constructor-injected dependencies (mock these via interfaces)
- Static calls, `DateTime.Now`, `Random` (flag for refactoring if untestable)
- External I/O (database, HTTP, file system)

If refactoring is needed for testability, propose the minimal changes first.

## Step 3: Generate Tests

For each meaningful behavior:
1. Write a test following **Arrange–Act–Assert**.
2. Name it `MethodName_Scenario_ExpectedOutcome`.
3. Cover: happy path, edge cases, error paths, boundary conditions.
4. Use the project's test framework and assertion library.
5. Mock only external dependencies. Never mock the code under test.

## Step 4: Validate

- Verify the tests compile.
- Run them. All must pass.
- Check that each test verifies a distinct behavior, not just that a mock was called.
