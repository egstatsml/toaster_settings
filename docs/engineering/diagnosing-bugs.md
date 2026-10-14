## What it does

`diagnosing-bugs` runs a six-phase diagnosis on a hard bug, a silent training failure, or a performance regression in research code: build a repro, minimise it, rank hypotheses, instrument, fix with a regression test, clean up.

It will not let the agent form a theory until a **tight** feedback loop exists: one named command, already run once, that goes red on *this* bug and green when it is fixed. Given "the loss goes NaN at step 40k", a coding agent by default reads code and guesses. This skill blocks that. No red-capable command, no Phase 2. Once the loop exists, everything after it (bisection, hypothesis-testing, instrumentation) is mechanical.

## When to reach for it

Type `/diagnosing-bugs`, or the agent reaches for it automatically when a task fits: "diagnose" or "debug this", something broken, throwing, failing, or slow, NaN losses, a model that will not learn, results that do not reproduce, out-of-memory errors.

It is slow and thorough on purpose, so reach for it on the hard ones, not a question you want answered in one message.

| Your situation | Where to go |
| --- | --- |
| A specific defect you can describe as a symptom (a crash, a NaN, a wrong number) | This skill |
| It trains but badly, the result won't reproduce, or the result is too good | This skill: silent failures are in scope |
| A throughput or memory regression with a known before-and-after | This skill, via its performance branch (measure a baseline, then bisect) |
| "Where are the bottlenecks?", no specific symptom | Not this skill: it diagnoses one known failure, it does not audit |
| Throwaway code to answer a research question, not chase a defect | [prototype](prototype.md) |
| Building a planned behaviour test-first | [tdd](tdd.md) |

## The tight loop is the skill

Phase 1 gets the most effort because it is the only hard phase. Ways to build the loop, roughly in order of preference:

1. A failing test at whatever seam reaches the bug (a data contract, a loss on a known input, a one-step training run).
2. A **tiny training run**: a few dozen steps on a handful of examples, on CPU if possible, asserting the symptom (loss finite, loss falls, gradients non-zero).
3. A **fixture replay**: the exact batch, checkpoint, or config that triggered the failure, saved and replayed in isolation. For a mid-run NaN, the batch and model state from the step before.
4. The training or evaluation entry point with a fixture config, diffed against a known-good snapshot.
5. A differential loop: two versions, two configs, or your code against a reference implementation, diffed layer by layer.
6. A bisection harness over commits, dataset versions, or library versions, for `git bisect run`.
7. A property loop across many seeds or inputs, for "sometimes wrong".
8. A human-in-the-loop script, last resort, for a step only you can take (logging into the cluster, reading a remote dashboard). The skill ships `scripts/hitl-loop.template.sh` so even that loop stays structured.

*A* loop is not the goal. **Tight** is: seconds not minutes, the same verdict every run, asserting your exact symptom, runnable by the agent on the hardware it has. For a flaky bug (multi-worker loading and distributed training are the usual sources), the target is a **higher reproduction rate**, not a clean repro. When no loop can be built, the agent must stop, list what it tried, and ask you for the GPU, the full dataset, a captured artifact, or permission to instrument a real run.

## Silent failures

Many research bugs throw nothing: the run finishes and the number is wrong. For those, the skill reaches for a fixed set of probes:

- **Overfit one batch**: if the loss won't go near zero on a handful of examples, the bug is wiring, not capacity.
- **Inspect a real batch** after every transform: labels aligned, augmentations sane, normalisation applied once.
- **Loss at initialisation** should match chance (`log(num_classes)` for cross-entropy).
- **Gradients** finite and non-zero where they should be; for NaNs, find the first non-finite layer.
- **Leakage**, when the result is too good.
- **Modes and seeds**, when the same config gives different results.

## The gates between phases

| Gate | What has to be true |
| --- | --- |
| Into Phase 2 | A named command, already run with its (redacted) output shown, that can go red on this bug |
| Into Phase 3 | The repro is reproduced *and* minimised: every remaining element is load-bearing |
| Into Phase 4 | 3 to 5 ranked, falsifiable hypotheses, each stating its prediction, shown to you before any is tested |
| Into Phase 5 | Probes map to a specific prediction, one variable at a time, every debug log tagged like `[DEBUG-a4f2]` |
| Done | The original repro no longer reproduces, instrumentation is gone, the commit message names the correct hypothesis, and any results produced by the buggy code are flagged for re-running |

Phase 5 writes the regression test before the fix, but only at a **correct seam**: one where the test exercises the real bug pattern as it occurs. Where the only seam is too shallow, the missing seam is itself the finding, recorded instead of papered over with a test that gives false confidence.

## Common questions

**It fires on quick questions where I just wanted a direct answer.**

It is model-invoked, so a plain description of a problem can trigger the full diagnosis, repro and all. Say what you want ("just answer this, don't diagnose"), or disable model invocation for it in your harness.

**Does it stop and ask me before it writes the fix?**

No. The only human checkpoint is the ranked hypothesis list in Phase 3, and the agent proceeds with its own ranking if you are away. If you want a gate before the fix, say so when you invoke the skill.

**Will the output it shows leak secrets?**

The skill tells the agent to redact every secret as `<REDACTED>`, build loops against environment variables rather than inline credentials, and quote only the lines of a captured log that carry the signal. Still glance at what it shows before it goes anywhere shared.

## It's working if

- It shows you a command and its red output before it offers a single theory.
- The failure it reproduces is the one you reported, not a nearby one it found on the way.
- The loop runs in seconds: a tiny model, a few steps, a replayed batch, not the full training run.
- It shows you 3 to 5 ranked hypotheses, each with a falsifiable prediction, before testing any.
- A grep for its `[DEBUG-...]` tag comes back empty when it declares done.
- It tells you which earlier results came from the buggy code and may need re-running.
- When it cannot lock the bug down with a test, it says so instead of writing a shallow one.

## Where it fits

`diagnosing-bugs` is a reach-for-it-anytime standalone, the "something's broken" on-ramp to the main flow. It keeps no state and needs no setup. [retro](retro.md) comes after it: run it in the same session once the fix is in, to ask what would have prevented the bug. Where the finding is that no good seam exists to lock the bug down, that is a job for [improve-codebase-architecture](improve-codebase-architecture.md). [ask](ask.md) routes across the whole set.
