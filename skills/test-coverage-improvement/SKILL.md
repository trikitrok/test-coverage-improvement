---
name: test-coverage-improvement
description: Assess and improve test protection of production behavior using direct and indirect coverage, characterization tests, coverage reports, and targeted mutation testing.
---

# Test Coverage Improvement

## Purpose

Tests are the executable evidence of observable behavior. Before a refactoring,
determine whether the behavior we want to preserve is adequately covered.

“Covered” does not mean merely that a line executed. Adequate coverage means
that the important observable outcomes and decision branches are verified by
meaningful assertions.

## What to inspect

Identify:

- the production behavior and its observable outcomes;
- all relevant branches, including success, failure, boundary, fallback, and
  stale/ignored paths;
- direct tests of the interface being changed;
- indirect tests that reach it through another interface;
- integration tests that verify important hand-offs or final state.

Indirect coverage is valid. Do not duplicate tests for an interface if existing
tests exercise its behavior sufficiently through another interface. Verify this
with coverage tools rather than assuming it.

## Before a refactoring

If the behavior is not adequately covered:

1. Add characterization tests for the existing behavior and make sure that they
   pass before changing production code. Never change production code while
   adding characterization tests; or
2. If improving the tests is substantial or requires a separate design
   decision, stop the refactoring and ask the user whether to handle test
   improvement as an independent task.

Do not begin a behavior-preserving refactoring merely because existing tests
pass.

## Coverage tools

Use the project’s coverage tooling to inspect relevant:

- statements;
- branches;
- functions;
- lines;
- observable outcomes.

Prefer targeted coverage for the production area under consideration. Be aware
that file-level metrics can be diluted by unrelated code in the same file.
Coverage percentages are evidence, not a universal acceptance threshold.

## Coverage gate before mutation testing

Run coverage analysis before mutation testing. Mutation testing is a slower
diagnostic step and is useful only after the relevant behavior has high branch
coverage.

If relevant branch coverage is low, do not run mutation testing yet. Mutations
in uncovered code can survive simply because no test executes them, so the
report will be dominated by noise and will not distinguish test weaknesses from
untested code. First add characterization tests or improve existing direct or
indirect tests, then rerun coverage.

“High” is contextual and does not necessarily mean 100%. Explain or exclude
branches that are unreachable, outside the observable contract, or belong to
an intentional legacy seam. Fowler defines a legacy seam as a place where the
system's behavior can be altered without editing the source code at that place;
such a seam may be deliberately left with limited coverage while legacy code
is being displaced. [Martin Fowler on legacy seams](https://martinfowler.com/bliki/LegacySeam.html)

Only when relevant branch coverage is high enough should you run targeted
mutation testing. Its purpose at that point is to find meaningful assertions
that are missing even though tests execute the branches, not to compensate for
untested code.

## Mutation testing

After the coverage gate passes, use targeted mutation testing to provide
additional evidence about the behavior under consideration. Mutate the
smallest relevant production scope and run the smallest relevant test scope.

Do wait for mutation testing to actually finish before reporting any result.

Use the host project’s mutation-testing documentation for command syntax,
argument rules, and interpretation of tool-specific results.

A mutant the checker rejects and a test that fails while Stryker is killing a
mutant are different outcomes. Report each with its own reason, such as a
compilation error or a timeout, and never merge them into a single mutation
score without explaining the distinction.

Review surviving mutants individually. For every survivor, classify whether it
indicates:

- a real missing behavioral assertion;
- a weak or overly permissive assertion;
- unreachable code;
- redundant or superfluous code;
- an intentional legacy seam;
- behavior that is not part of the contract.

Report relevant surviving mutants to the user with their location, mutation,
likely meaning, and possible action. Do not blindly add tests to achieve a
higher mutation score. Surviving mutants can reveal code that should be
simplified or removed rather than behavior that should be permanently encoded
in tests. [Codesai’s mutation-testing article](https://codesai.com/posts/2026/04/agents-blindly-killing-mutants)

## Test quality

Prefer tests that:

- assert observable outcomes rather than implementation details;
- cover meaningful input boundaries and branches;
- reuse existing builders and test helpers;
- test through the appropriate public or registered interface;
- avoid duplicating coverage already provided by stronger integration tests;
- avoid test doubles for types the application does not own when an integration
  boundary is available.

Do not weaken production design or add brittle tests merely to kill a mutant.

## Decision gate

Conclude with one of:

- **Adequately covered:** the relevant behavior and branches are asserted, and
  coverage/mutation evidence found no unexplained relevant gaps.
- **Covered with accepted survivors:** remaining survivors are explained as
  unreachable, superfluous, legacy, or outside the contract; report them to the
  user.
- **Insufficiently covered:** add characterization tests or ask to separate test
  improvement from the planned production change.

When the result is insufficiently covered, do not proceed with a
behavior-preserving refactoring until the user decides how to address the gap.
