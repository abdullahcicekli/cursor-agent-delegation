---
name: testing-standards
description: Generic testing patterns — what to test, how to structure, when to mock. Language-agnostic.
estimated-tokens: 1600
used-by: [engineer, coder, reviewer]
---

# Testing Standards

## What to test

- **Public surface** of a module — its declared contract, not its internals.
- **Behavior under failure** — what happens on bad input, network failure, dependency error.
- **Boundary conditions** — empty, one, many; min, max, off-by-one.
- **Regressions** — once a bug is fixed, lock the fix with a test.

## What NOT to test

- Trivial getters / setters / pass-through wrappers.
- Framework code (the framework is already tested).
- Generated code.
- Implementation details that will change on every refactor (private helpers, internal data shape).

## Structure: Arrange-Act-Assert

```
test "case description" {
  // arrange — set up inputs and dependencies
  // act — invoke the unit under test
  // assert — verify exactly one behavior
}
```

One assertion per test where reasonable. Multiple related asserts on the same act are fine.

## Naming

- **Test name**: `<unit>__<scenario>__<expected_outcome>` or "should _" depending on language idiom.
- **Examples**:
  - `getUserById__withMissingId__returnsNotFound`
  - `should reject negative quantities`
  - `OrderService_TotalsCorrectlyAcrossCurrencies`

## Test pyramid

| Layer | Cost | Coverage goal |
|---|---|---|
| Unit | cheap, fast | most behavior |
| Integration | moderate | module boundaries, real DB / queue, contract tests |
| E2E / system | expensive | golden paths, smoke checks |

Don't invert the pyramid (lots of E2E, few unit) — slow and flaky.

## Mocking discipline

- **Mock at the boundary** — external services, time, randomness, file system.
- **Don't mock what you own** — if you mock your own classes everywhere, the test verifies your mock, not your code.
- **Prefer fakes / in-memory implementations** over fragile mock-call assertions.
- **Test doubles** make a test fast; they don't make it correct. Real dependency wherever practical.

## Determinism

- No `sleep` / wall-clock waits — use injected clocks or framework-provided fake timers.
- No real network calls in unit tests.
- No database reliance unless the test is explicitly an integration test.
- Random / time / IDs — inject so they can be controlled in tests.

## Coverage

- Coverage is a smoke signal, not a goal. Focus on behavior coverage, not line coverage.
- A 100% line-covered file with no failure-mode tests is dangerous. A 70% line-covered file with strong contract tests is fine.

## Anti-patterns

- **Tautological tests**: re-asserting the implementation in different words.
- **Snapshot-only tests**: snapshots without context become rubber-stamps.
- **Shared mutable test state**: tests must be independently runnable.
- **Multi-purpose tests**: one test, one behavior.
