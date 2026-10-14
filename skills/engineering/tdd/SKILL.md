---
name: tdd
description: Test-driven development for research code. Use when the user wants to build features or fix bugs test-first, mentions "red-green-refactor", or wants tests for data pipelines, models, losses, or metrics.
---

# Test-Driven Development

TDD is the red → green loop. This skill is the reference that makes that loop produce tests worth keeping: what a good test is, where tests go, the anti-patterns, and the rules of the loop. Every section applies on every cycle: consult them before and during the loop, not after.

When exploring the codebase, read `GLOSSARY.md` (if it exists) so test names and interface vocabulary match the project's domain language, and respect ADRs in the area you're touching.

## What a good test is

Tests verify behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't. A good test reads like a specification: "loader yields batches with no example from the test split" tells you exactly what capability exists, and it survives refactors because it doesn't care about internal structure.

See [tests.md](tests.md) for examples and [mocking.md](mocking.md) for mocking guidelines.

## What to test in research code

Test the code, not the research. A test can show that the pipeline, model, and metrics do what they claim; it cannot show that the method works, and it should never try (no "accuracy is above 0.9" assertions on real training).

Behaviours worth a test, roughly in order of how often they catch real bugs:

- **Data contracts**: shapes, dtypes, value ranges, and label alignment of what the pipeline yields; splits that are disjoint; augmentations applied only where intended.
- **Known-answer computations**: a loss or metric on a tiny hand-worked input whose answer you computed independently (on paper, or from the paper's definition).
- **Invariances and equivalences**: masking padding leaves the output unchanged, a batched forward pass equals per-example passes, a permutation-invariant model is permutation invariant.
- **Gradient flow**: every parameter that should train receives a non-zero gradient after one backward pass; frozen ones receive none.
- **Overfit a tiny batch**: a few dozen steps on a handful of examples drive the loss close to zero. The cheapest end-to-end check that model, loss, and optimiser are wired together.
- **Determinism**: two runs with the same seed and config produce the same outputs (within tolerance on GPU).
- **Checkpoint round-trip**: save, load, and get the same outputs and optimiser state back.

Keep every test fast: tiny fixtures, a few steps, CPU. Compare floats with an explicit tolerance, never exact equality.

## Seams: where tests go

A **seam** is the public boundary you test at: the interface where you observe behavior without reaching inside. Tests live at seams, never against internals.

**Test only at pre-agreed seams.** Before writing any test, write down the seams under test and confirm them with the user. No test is written at an unconfirmed seam. You can't test everything, so agreeing the seams up front is how testing effort lands on the critical paths and complex logic instead of every edge case.

Ask: "What's the public interface, and which seams should we test?" Give each proposed seam a one-line note on what it catches and what it misses.

When the shape of that interface is itself in question (how deep the module is, where the seam belongs, what the interface should expose), call the Skill tool with "codebase-design" for the vocabulary. It is the shared source of the module, interface, depth, seam, adapter, leverage and locality terms, and it is a reference to consult, not a session to run.

## Anti-patterns

- **Implementation-coupled**: mocks internal collaborators, tests private methods, or verifies through a side channel (querying the database instead of using the interface). The tell: the test breaks when you refactor but behavior hasn't changed.
- **Tautological**: the assertion recomputes the expected value the way the code does (`assert loss(x, y) == ((x - y) ** 2).mean()` when that is the implementation, a snapshot saved from the code's own output, a constant asserted equal to itself), so it passes by construction and can never disagree with the code. Expected values must come from an independent source of truth: a known-good literal, a worked example, the paper's definition, a reference implementation.
- **Horizontal slicing**: writing all tests first, then all implementation. Bulk tests verify _imagined_ behavior: you test the _shape_ of things rather than observable behavior, the tests go insensitive to real changes, and you commit to test structure before understanding the implementation. Work in **vertical slices** instead: one test → one implementation → repeat, each test a **tracer bullet** that responds to what the last cycle taught you.

## Rules of the loop

- **Red before green.** Write the failing test first, then only enough code to pass it. Don't anticipate future tests or add speculative features.
- **One slice at a time.** One seam, one test, one minimal implementation per cycle.
- **Refactoring is not part of the loop.** It belongs to the review stage (see the `code-review` skill), not the red → green implementation cycle.
