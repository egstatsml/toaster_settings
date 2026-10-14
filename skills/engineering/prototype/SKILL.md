---
name: prototype
description: Build a throwaway prototype (a script or notebook) to answer one research or design question fast. Use when the user wants to check whether an idea learns at all on a toy problem, look at what some data or a model's behaviour actually looks like, or sanity-check a module's logic or interface before building it properly.
---

# Prototype

A prototype is **throwaway code that answers a question**. The question decides the shape.

## Pick a branch

Identify which question is being answered, using the user's prompt, the surrounding code, or by asking if the user is around:

- **"Does this idea work at all?"** → a **toy experiment**. The smallest problem that can show the effect: synthetic data, a tiny subset, a two-layer model, minutes on a laptop. It prints or plots the one signal that answers the question (does the loss fall, does the new term change the behaviour, does the baseline beat it). If the effect only shows at scale, say so and stop: that is an experiment for the real code, not a prototype.
- **"What does this look like?"** → an **exploratory notebook or script** over real data or a real checkpoint: distributions, examples, attention maps, failure cases, embeddings. It produces plots and summaries a human looks at.
- **"Does this logic or interface feel right?"** → a **pure module plus a driver**. Put the logic (a loss, a sampler, a masking scheme, a config shape) in a small pure module that could be lifted into the real codebase, and drive it from a script that pushes it through the awkward cases (empty batch, all padding, one class) and prints the result of each.

State the question in one line at the top of the prototype before writing any code. A prototype that answers the wrong question is pure waste.

## Rules for every branch

1. **Throwaway from day one, and clearly marked as such.** Put it under `prototypes/<name>/` (or follow the repo's existing convention), so nobody mistakes it for library code. It may import from the real codebase; the real codebase never imports from it.
2. **Trivial to run.** One command (`python prototypes/<name>/run.py`, or one notebook run top to bottom). No setup the user has to think about.
3. **Small and seeded.** Seed every source of randomness, and keep it fast enough to rerun on every change. If it needs a GPU or the full dataset, the question is probably too big for a prototype.
4. **Skip the polish.** No tests, no config system, no error handling beyond what makes it runnable, no abstractions. The point is to learn something fast.
5. **Surface the signal.** Print or plot the thing that answers the question after every run, so the user can see the answer without reading the code.
6. **Capture it when done.** Write the verdict (the question, the answer, and the run that showed it) into the plan it feeds (`plans/<slug>/spec.md` under Further Notes, or the relevant ticket's Notes). Fold any validated logic into the real code properly, with tests. Then keep the prototype itself as a **primary source** by committing it to a `prototype/<name>` branch out of main, and point to that branch from the verdict. Main keeps only the validated decision.

## Anti-patterns

- **Don't add tests.** A prototype that needs tests is no longer a prototype.
- **Don't generalise.** No "what if we wanted to support X later." The prototype answers one question.
- **Don't treat a prototype result as a research result.** One seed on a toy problem is evidence that an idea is worth building, not evidence that it works.
- **Don't promote the prototype directly into the codebase.** It was written under prototype constraints. Rewrite it properly when you fold it in.
