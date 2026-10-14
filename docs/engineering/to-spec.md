## What it does

`to-spec` turns the conversation you have just had into a **spec** for a piece of research code (a model, an experiment, a data pipeline, an evaluation) and saves it as `plans/<slug>/spec.md`.

It does not interview you. By the time you reach for it the deciding is done, so it synthesises what is already known (the thread, the codebase, your `GLOSSARY.md` and ADRs) instead of starting a new round of questions. The spec records decisions you made; it is not a place to make new ones.

## When to reach for it

You invoke this by typing `/to-spec`, and the agent won't reach for it on its own.

Reach for it when the build is too big for one session and has to be split across several:

| Where you are | What to run |
| --- | --- |
| Nothing decided yet | [grill-with-docs](grill-with-docs.md) first |
| Decided, and the work fits one context window | [implement](implement.md): skip the spec |
| Decided, and the work spans several sessions | `/to-spec`, then [to-tickets](to-tickets.md) |

## Prerequisites

It writes into the repo you are working on, creating `plans/<slug>/` if needed and telling you the path.

## The spec is a decision record

Context windows end. While grilling you settled the research question, the hypothesis, the data and splits, the baselines, the metrics and the compute, and what you deliberately refused. All of that lives in one conversation you are about to clear. The spec keeps it, in your project's vocabulary, so a fresh session can build from it without you explaining again. Anything in it you never said is a defect.

The template is shaped for research: a hypothesis stated so it can be wrong, one entry per experiment with its baselines, metrics and seeds, plus sections for reproducibility and compute budget.

## Seams before prose

Before writing, `to-spec` sketches the **seams** the code will be tested at and checks them with you. It prefers existing seams, picks the highest one it can, and aims for as few as possible (ideally one). In research code these are usually the data pipeline's output (batches, shapes, dtypes, splits), a model's forward pass, a loss or metric, and a short training run on a tiny fixture.

Those seams carry downstream: [tdd](tdd.md) writes tests only at agreed seams, and [code-review](code-review.md) checks the diff against the spec. Take the seam conversation seriously here rather than leaving it to implementation.

## Common questions

**Why not go straight from grilling to `/to-tickets`?**

On single-session work, skip both and go to [implement](implement.md). The spec earns its step on multi-session work because tickets are disposable and the spec is not: it stays as the one place recording the reasoning behind them.

**Is the spec for me to review, or just for the agent?**

Mostly for the agent, so it reads dense. Read the hypothesis, the seams and the out-of-scope section: those are where a wrong decision is cheapest to catch now. If the spec surprises you, the grilling was too shallow.

**Do I keep the spec in sync once tickets start?**

Nothing keeps it in sync; treat it as a snapshot of what you knew. Lasting knowledge belongs in `GLOSSARY.md` and ADRs, not in an edited spec.

## It's working if

- It starts writing instead of asking you new questions.
- It shows you the seams before writing and proposes as few as it can.
- The hypothesis names a result that would refute it.
- You remember making every decision in it, and the out-of-scope section lists real things you refused.
- It tells you the `plans/<slug>/spec.md` path at the end.

## Where it fits

`to-spec` is a **chain step** on the multi-session branch of the main flow (`grill-with-docs → to-spec → to-tickets → implement → code-review`). Upstream, [grill-with-docs](grill-with-docs.md) makes the decisions this skill only records. Downstream, [to-tickets](to-tickets.md) cuts the spec into tracer-bullet tickets; run it in the same context window. When unsure which flow fits, [ask](ask.md) routes you.
