---
name: tdd
description: Test-driven development. Use when the user wants to build features or fix bugs test-first, mentions "red-green-refactor", or wants integration tests.
---

# Test-Driven Development

TDD is the red → green → refactor loop. This skill is the reference that makes that loop produce tests worth keeping: what a good test is, where tests go, the anti-patterns, and the rules of the loop. Every section applies on every cycle: consult them before and during the loop, not after.

When exploring the codebase, read `GLOSSARY.md` (if it exists) so test names and interface vocabulary match the project's domain language, and respect ADRs in the area you're touching.

## What a good test is

Prefer tests through interfaces used by callers. A good test reads like a specification: "user can checkout with valid cart" tells you exactly what capability exists. Its assertions describe the contract rather than the current arrangement of collaborators.

See [tests.md](tests.md) for examples and [mocking.md](mocking.md) for mocking guidelines.

## Seams: where tests go

A **test seam** is an interface where a test controls inputs and observes behavior. It can be external to a module or an internal interface with a meaningful contract. Use the repository's domain vocabulary and existing test conventions.

Inspect the affected code and tests, then record the selected seams and what they cover or miss. Reuse seams already agreed in the task contract. Select routine seams autonomously; ask when the choice would materially change architecture, coverage, or cost, or when the user explicitly requires approval. Revisit provisional seams as inspection reveals constraints.

Choose test scope for the risk. Prefer caller-facing behavior tests, with focused tests at stable internal interfaces when they provide clearer, faster evidence for important algorithms or failure cases. Direct persistence inspection is appropriate when storage behavior is itself the contract; explain that purpose rather than treating a database query as inherently invalid.

When the shape of the interface is itself in question (how deep the module is, where the seam belongs, what the interface should expose), consult `codebase-design` through the available skill mechanism. It is a reference to consult, not a session to run. Keep repository terminology when applying its guidance.

## Anti-patterns

- **Implementation-coupled**: asserts incidental private structure, collaborator calls, or storage details that the contract does not require. The tell: the test breaks when you refactor but observable behavior hasn't changed.
- **Tautological**: the assertion recomputes the expected value the way the code does (`expect(add(a, b)).toBe(a + b)`, a snapshot derived by hand the same way, a constant asserted equal to itself), so it passes by construction and can never disagree with the code. Expected values must come from an independent source of truth: a known-good literal, a worked example, the spec.
- **Horizontal slicing**: writing all tests first, then all implementation. Bulk tests verify _imagined_ behavior: you test the _shape_ of things rather than user-facing behavior, the tests go insensitive to real changes, and you commit to test structure before understanding the implementation. Work in **vertical slices** instead: one test → one implementation → repeat, each test a **tracer bullet** that responds to what the last cycle taught you.

## Rules of the loop

- **Observe red.** Write a focused test and run it against the pre-change behavior. Confirm it fails because the required behavior is missing or wrong. Fix syntax errors, broken fixtures, and setup failures until the failure demonstrates the intended gap. If it already passes, investigate before changing production code. For a bug, reproduce its trigger and demonstrate the regression test fails without the fix.
- **One slice at a time.** One seam, one test, one minimal implementation per cycle.
- **Make it green.** Implement only enough behavior to satisfy the current slice, then run the test and nearby regression checks. Add the next behavior in the next cycle.
- **Refactor after green.** Improve names, structure, and duplication in small behavior-preserving steps, rerunning relevant tests after each coherent change. Keep broader redesign within the task's agreed scope.
- **Preserve evidence.** Record the red failure reason and the green command/result concisely in the task's existing notes or handoff. When test-first execution is infeasible, report why and provide other observable verification; distinguish new regression tests from characterization tests that document existing behavior.
