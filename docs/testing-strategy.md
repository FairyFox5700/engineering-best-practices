# Testing Strategy

## The test pyramid

```
        /\
       /  \      E2E (few)       — slow, brittle, high confidence end-to-end
      /----\
     /      \    Integration     — medium speed, tests component boundaries
    /--------\
   /          \  Unit (many)     — fast, isolated, cheap to write and run
  /------------\
```

**Rough target ratio:** ~70% unit, ~20% integration, ~10% E2E. Exact numbers vary by domain (e.g., UI-heavy apps lean more E2E/visual), but the shape — many fast cheap tests at the bottom, few slow expensive ones at the top — should hold.

**Why the shape matters:** inverting the pyramid (an "ice cream cone" — mostly E2E, few unit tests) gives you slow feedback loops, flaky suites, and hard-to-localize failures. A failing unit test tells you exactly what broke; a failing E2E test tells you something in a 10-step user journey broke, somewhere.

## What belongs at each layer

- **Unit tests:** a single function/class/module, dependencies mocked/stubbed, no network/disk/DB. Should run in milliseconds. Target: every new piece of logic gets a unit test before or alongside the implementation.
- **Integration tests:** verify that two or more real components work together correctly (e.g., your repository layer against a real test database, your HTTP client against a real API contract). Slower, but catch issues unit tests with mocks can't (wrong SQL, serialization mismatches).
- **E2E tests:** simulate a real user journey through the whole system (browser automation, full API round-trips). Expensive to write and maintain — reserve for critical user paths (login, checkout, core workflow), not every possible permutation.

## Test naming conventions

Make the test name describe behavior, not implementation:
```
# Good
test_login_fails_with_expired_token()
test_checkout_applies_discount_when_coupon_valid()

# Bad
test_1()
test_login()
```
Common pattern: `MethodOrBehavior_Scenario_ExpectedResult` or Given/When/Then-style naming for BDD-style suites.

## Mutation testing (optional, high-maturity teams)

Tools like [Stryker](https://stryker-mutator.io/) (JS/.NET) or [mutmut](https://github.com/boxed/mutmut)/[Cosmic Ray](https://cosmic-ray.readthedocs.io/) (Python) deliberately introduce small bugs ("mutants") into your code and check whether your test suite catches them. Useful for validating that high coverage numbers reflect *meaningful* assertions, not just lines executed. Not something to run on every PR — run periodically (weekly/monthly) as a test-suite-quality audit, since it's computationally expensive.

## Coverage as a floor, not a target

- Use code coverage to catch obviously untested code paths, not as a quality metric to game.
- A reasonable CI gate: don't let PRs *decrease* coverage on changed files below the existing baseline.
- 100% coverage with weak assertions (e.g., only checking "did not throw") is worse than 80% coverage with meaningful behavioral assertions. Mutation testing (above) is a better signal of real test quality than raw coverage %.

## Checklist for new features

- [ ] Unit tests cover new logic, including edge cases and error paths
- [ ] Integration test added if the change crosses a component/service boundary
- [ ] E2E test added/updated only if this is a critical user journey
- [ ] Tests included in the same PR as the implementation (see [commits-and-pull-requests.md](./commits-and-pull-requests.md) — Google explicitly requires test code to be part of the same CL)
- [ ] No new flaky tests introduced (run the new/changed tests multiple times locally before pushing)
