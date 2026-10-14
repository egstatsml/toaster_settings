## What it does

`tdd` builds research code test-first: one failing test, then just enough code to pass it, then the next behaviour. It carries the standards that make that loop produce tests worth keeping: what a good test is, what is worth testing in a pipeline or model, where tests go, what mocks are for, and the anti-patterns that make a suite worthless.

It tests the code, never the research. A test can show that the data pipeline, model, loss, and metrics do what they claim; it cannot show that the method works, so you will never see "accuracy is above 0.9" asserted on a real training run. It also writes no test at a seam you have not agreed to first. `tdd` is a **reference**, not a driver: it holds the rules of the loop, and something else (you, or [implement](implement.md)) runs the session that applies them.

## When to reach for it

Type `/tdd`, or the agent reaches for it automatically when a task fits: building a feature or fixing a bug test-first, saying "red-green-refactor", or asking for tests on a data pipeline, model, loss, or metric.

| Your situation | Where to go |
| --- | --- |
| A concrete behaviour with an input and an observable output (a loader, a loss, a metric, a masking rule) | `tdd` |
| The behaviour isn't pinned down yet | [to-spec](to-spec.md), which also agrees the test seams before any code is written |
| The question is really the shape of the interface, not the tests | [codebase-design](codebase-design.md) |
| You have a spec or tickets and want the whole build run for you | [implement](implement.md), which drives `tdd` per ticket |
| You want to know whether an idea learns at all | [prototype](prototype.md): that is a question for throwaway code, not a test |

## What is worth a test in research code

Roughly in order of how often they catch real bugs:

- **Data contracts**: shapes, dtypes, value ranges, label alignment, disjoint splits, augmentations only where intended.
- **Known-answer computations**: a loss or metric on a tiny input whose answer you worked out independently, on paper or from the paper's definition.
- **Invariances and equivalences**: padding masked out leaves the output unchanged; a batched forward pass equals per-example passes.
- **Gradient flow**: every trainable parameter gets a non-zero gradient after one backward pass; frozen ones get none.
- **Overfit one batch**: a few dozen steps on a handful of examples drive the loss near zero. The cheapest end-to-end check that model, loss, and optimiser are wired together.
- **Determinism** and **checkpoint round-trip**: same seed, same outputs (within tolerance); save then load gives the same outputs and optimiser state.

Every test stays fast: tiny fixtures, a few steps, CPU, floats compared with an explicit tolerance.

## The loop, and the seam it runs at

**Red-green.** Write the failing test, then only enough code to pass it. There is no refactor phase: refactoring belongs to [code-review](code-review.md), in a separate session.

**Vertical slice.** One test at one seam, then the minimal implementation, then repeat. Each test is a **tracer bullet** that responds to what the last cycle taught you. Writing all the tests first verifies *imagined* behaviour.

**Pre-agreed seam.** A seam is the public boundary you observe behaviour at without reaching inside. Before any test exists, `tdd` proposes the seams, each with a one-line note on what it catches and what it misses, and waits for your confirmation. In the full chain the seams are agreed earlier, in [to-spec](to-spec.md).

| Anti-pattern | The tell |
| --- | --- |
| Implementation-coupled | The test breaks on a refactor though behaviour did not change: spies on your own layers, asserted call counts, private helpers tested directly. |
| Tautological | The expected value is computed the way the code computes it (`mse(x, y) == ((x - y) ** 2).mean()`), so it passes by construction. Expected values come from a hand-worked literal, the paper's definition, or a reference implementation. |
| Horizontal slicing | A batch of tests landed before any implementation. |

Mocks are for system boundaries only: experiment trackers, remote storage and model hubs, job launchers, sometimes the filesystem. Never mock your own models, losses, or transforms, and never mock the tensor library. A ten-example dataset and a two-layer model with hidden size 8 beat a mock.

## Common questions

**Why doesn't it refactor? The description says "red-green-refactor".**

The phrase still fires the skill, but what you get is red-green. Agents rarely performed the refactor step, and review works better as its own session, so refactoring lives in [code-review](code-review.md).

**It wrote the implementation before the test, even though the skill says red first.**

It happens; no instruction makes an agent comply every time. The loop is still worth running when adherence is imperfect. If strict red-first matters for a particular slice, watch the run rather than trusting the skill to enforce it.

**Can a test check that my model actually works?**

No. A results assertion on real training is slow, flaky, and tests the method rather than the code. That belongs in an experiment and its write-up. The overfit-one-batch test is the closest the suite gets: it proves the pieces are wired together, not that the method generalises.

## It's working if

- It names the seams it intends to test at, with what each catches and misses, and waits before any test file exists.
- One test appears, goes red, gets just enough code to pass, and only then does the next test appear.
- Test names read as capabilities ("loader never yields test split examples"), not internals ("forward calls attention").
- Expected values are literals you can trace to a hand calculation or the paper, and float comparisons carry a tolerance.
- The suite runs in seconds on CPU, and renaming an internal function breaks nothing.
- Mocks appear only around trackers, storage, and launchers, never around your own modules.

## Where it fits

`tdd` runs inside the build step of the main chain rather than as a step of its own: `grill-with-docs → to-spec → to-tickets → implement → code-review → retro`. [to-spec](to-spec.md) agrees the seams up front, [implement](implement.md) drives `tdd` per ticket, and [code-review](code-review.md) checks afterwards that only agreed seams were used and owns the refactoring. [codebase-design](codebase-design.md) supplies the seam and deep-module vocabulary. You can also reach for it on its own whenever there is a concrete behaviour to build and no full spec in play. When you are unsure which skill fits, [ask](ask.md) routes you.
