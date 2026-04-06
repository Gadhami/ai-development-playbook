---
description: '.NET testing standards'
applyTo: '**/*Tests*/**/*.cs'
---

# .NET Testing Standards

## Test or Skip?

**Test:** Business logic, algorithms, validation, conditional behavior, data transformation, orchestration of multiple operations, complex queries with filtering/sorting.

**Skip:** DTOs/POCOs, constructors that only assign fields, single-line pass-through methods, guard clauses, trivial LINQ projections, configuration/DI registration.

## Unit vs Component Tests

**Unit test:** Tests a single class in isolation. All dependencies are mocked. Lives in `*.Tests.Unit` projects.

**Component test:** Tests multiple real classes working together (e.g., a handler + validator + repository with an in-memory database). Lives in `*.Tests.Component` projects.

**Choose correctly:**
- If you mock all dependencies → unit test. Put it in the Unit test project.
- If you use real implementations, in-memory databases, or `WebApplicationFactory` → component test. Put it in the Component test project.
- Never mix: a test that mocks some dependencies but uses real ones for others is usually a component test.

## Structure

- One test class per production class. Mirror the source folder structure.
- **Arrange–Act–Assert.** One behavior per test. No branching inside tests.
- Naming: `MethodName_Scenario_ExpectedOutcome`.
- Use nested classes to group tests by method when the test file grows large.
- No shared mutable state between tests. Each test sets up its own data.

## Assertions

- Use FluentAssertions (or the project's chosen assertion library) exclusively.
- `using (new AssertionScope())` when making multiple related assertions.
- Assert specific, meaningful values — not just `Should().NotBeNull()`.
- For exceptions: `act.Should().ThrowAsync<SpecificException>().WithMessage(...)`.

## Mocking

- Mock only external dependencies through interfaces. Never mock the code under test.
- Prefer `MockBehavior.Strict` to catch unexpected interactions.
- Avoid mocks when a simple stub, fake, or in-memory implementation suffices.
- Verify interactions only when the interaction IS the behavior being tested (e.g., "sends an email"). Don't verify internal method calls.

## Test Data

- Deterministic data only. No `DateTime.Now`, no `Random`, no `Guid.NewGuid()` in assertions.
- Inject time via `TimeProvider` (or `IClock`). Inject randomness via an interface.
- Use builder or factory patterns for complex test objects.
- Avoid disk I/O; use in-memory alternatives.

## Workflow

- Write one test at a time. Make it pass. Then write the next.
- After all new tests pass, run the full affected test suite to catch regressions.
- Measure coverage to find gaps, not as a target to game.

## Parameterized Tests

- Use `[Theory]` + `[InlineData]` (xUnit), `[TestMethod]` + `[DataRow]` (MSTest), or `[TestCase]` (NUnit) for testing the same behavior with multiple inputs.
- Each data row should test a meaningfully different scenario.
- **Actively look for opportunities to consolidate:** if two or more tests share the same Arrange/Act/Assert structure and only differ in input values or expected results, combine them into a single parameterized test.
- Prefer `[DataRow]`/`[InlineData]` over separate test methods for input variations (valid/invalid strings, boundary values, enum members, null vs empty, etc.).
- Keep each data row self-descriptive — use the `DisplayName` parameter when the inputs alone don't make the scenario obvious.
- Do NOT force-merge tests that have genuinely different setup, assertions, or behavior paths — only merge when the structure is truly identical.

## Anti-Patterns to Avoid

- Tests that only verify a mock was called without checking outputs or side effects.
- Tests that duplicate the production logic (recomputing the expected value).
- Excessive setup that obscures what the test actually verifies.
- Tests coupled to implementation details (private method names, internal ordering).
