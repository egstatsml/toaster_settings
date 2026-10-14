## What it does

`prototype` writes **throwaway code that answers a question**: does this idea learn at all on a toy problem, what does this data actually look like, does this loss behave on the awkward cases. The question comes first and decides the shape of the script or notebook. A prototype that answers the wrong question is wasted, however clean it looks.

Throwaway is a constraint on how the code is *written*, not a promise to destroy it. No tests, no config system, no error handling beyond what makes it run, no abstractions, because none of that helps you learn the one thing you are trying to learn. Two things survive: the verdict goes into the plan, and the prototype goes onto a branch out of main as evidence for the verdict.

## When to reach for it

Type `/prototype`, or the agent reaches for it automatically when a task fits.

Reach for it the moment a question can't be settled by talking: you can't tell whether a new loss term does anything until you see it on synthetic data, or you can't reason about a dataset until you've looked at its label distribution and failure cases. Grilling sessions grow long on these questions; stop, build the throwaway version, look at the output, then answer in one line. If instead something already built is misbehaving and you want to know why, use [diagnosing-bugs](diagnosing-bugs.md): prototyping explores what to build, not why the built thing is broken.

## Three branches

The question picks the branch, and each branch produces a different artifact:

| Question | Artifact |
| --- | --- |
| "Does this idea work at all?" | A **toy experiment**: synthetic data or a tiny subset, a tiny model, minutes on a laptop, printing or plotting the one signal that answers the question. If the effect only shows at scale, it says so and stops. |
| "What does this look like?" | An **exploratory script or notebook** over real data or a real checkpoint: distributions, examples, embeddings, failure cases, as plots and summaries you look at. |
| "Does this logic or interface feel right?" | A **pure module plus a driver**: the logic (a loss, a sampler, a masking scheme, a config shape) in a small module you could lift into the real code, driven through the awkward cases (empty batch, all padding, one class). |

Every branch states the question in one line at the top, runs with one command, seeds every source of randomness, and prints the answer after each run. It lives under `prototypes/<name>/`; it may import from the real codebase, but the real codebase never imports from it.

## The prototype is a primary source

A finished prototype leaves two things, and they go to different places. The **verdict** (the question, the answer, and the run that showed it) goes into the plan it feeds: `plans/<slug>/spec.md` or the relevant ticket. Any validated logic is rewritten properly, with tests, in the real code.

The **prototype** itself is the runnable evidence, so it is not deleted. It doesn't belong in main either, because nobody maintains it and it goes stale fast. It is committed to a `prototype/<name>` branch out of main, never merged, and the verdict points at that branch.

## Common questions

**I got a nice curve on the toy problem. Does the idea work?**
No. One seed on a toy problem is evidence that the idea is worth building, not evidence that it works. The real experiment belongs in the real code, with real splits and more than one seed.

**An agent told me to `/prototype` when I should have been implementing.**
`prototype` is an appealing word, so agents sometimes reach for it as "the obvious next step" once tickets exist. If you already know what to build, the next step is [implement](implement.md). Reach for a prototype only when a specific question is unresolved and talking won't resolve it.

**How do I run it in its own session?**
A prototype lives in its own directory and generates a lot of context you don't want in the thread that asked the question. Use [handoff](../productivity/handoff.md) to move the work into a separate session and bring only the verdict back.

**It needs a GPU and the full dataset. Is that still a prototype?**
Probably not. If the question can't be answered small, it's too big for a prototype; cut it down to a smaller question, or build it as real code.

## It's working if

- The question is written in one line at the top of the script or notebook, not just in your head.
- One command reruns it, and the answer is printed or plotted without reading the code.
- It is answered in one sitting. If you're still building it a day later, the question was too big; split it.
- When it's over, main contains the decision and none of the prototype, and the plan points at the branch that still holds it.

## Where it fits

`prototype` is a **reach-for-it-anytime standalone**, and the detour in the main flow when a question needs a runnable answer. Upstream, [grill-with-docs](grill-with-docs.md) and [grill-me](../productivity/grill-me.md) settle the questions talk can settle; the rest come here, and the one-line verdict goes back into the interview. Downstream, the verdict becomes settled input for [to-spec](to-spec.md). For which skill fits a situation, [ask](ask.md) routes you over the whole set.
