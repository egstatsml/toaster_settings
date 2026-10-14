## What it does

`ask` is the router over the skills in this repo. You describe the situation you are in (a research idea you cannot start, a training run that went NaN, a session that has run long), and it names the skill or sequence of skills that fits, plus where your decisions in that sequence sit.

It recommends and stops. It does not grill, write a spec, open a file or fire the skill it just named; what you get back is the next thing to type, and you type it. It is a hand-written map of this repo's skills, not a scan of what you have installed.

## When to reach for it

You invoke this by typing `/ask`, and the agent won't reach for it on its own.

| Your situation | What the router gives back |
| --- | --- |
| An idea, and no idea where to start | The head of the main flow, and whether the build is small enough to skip the spec |
| Something broken (a NaN, a model that won't learn, a result that won't reproduce) | The [diagnosing-bugs](diagnosing-bugs.md) on-ramp |
| A question the literature or a library has to answer | The [research](research.md) on-ramp |
| Two skills that look interchangeable | The one concrete test that separates them, such as [grill-me](../productivity/grill-me.md) versus [grill-with-docs](grill-with-docs.md) turning on whether there is a repo |
| A long session and a decision about the context | The ordered tree over the five options at a phase boundary |
| A skill you have already picked | Nothing useful. Invoke that skill directly. |

## Flows, not skills

The leading word is **flow**: a path *through* the skills. When you name your situation, the router places you at a step on a flow rather than matching your keywords to one skill.

- **The main flow**, idea to code you trust: grill, then (for a multi-session build) spec and tickets under `plans/<slug>/`, implement one ticket per fresh context, review, then [retro](retro.md). Its two branches are a [prototype](prototype.md) detour for questions that need runnable code, and the spec-and-tickets split, worth its cost only when the build spans sessions.
- **On-ramps**, situations that generate work and merge onto the main flow: something broken, or a question for the papers and docs.
- **Codebase health**: [improve-codebase-architecture](improve-codebase-architecture.md) surveys for deepening opportunities, each of which re-enters the main flow as an idea.
- **Standalones** used on their own, and **a vocabulary layer** ([domain-modeling](domain-modeling.md), [codebase-design](codebase-design.md)) the other skills pull in.

The flow stops at code verified on small inputs. Launching the real training runs and sweeps stays with you.

## The phase boundary

A phase is a chunk of work inside a session (the grilling, the implementation, the debugging), and the boundary between two of them is the only place to decide what to do with the context. Mid-phase, continue or split what is left into subagents.

| Option | Take it when |
| --- | --- |
| **Continue** | The next phase wants this one verbatim, or you have smart zone left. Rule it out first |
| **`/clear`** | Everything behind you is disposable |
| **[handoff](../productivity/handoff.md)** | Something has to travel: a new harness, a new directory (a cluster checkout), a collaborator, a side task forked mid-phase |
| **Subagent** | The task is scoped tightly enough to run without you |
| **`/compact`** | None of the above. The default, at the bottom of the tree |

## Common questions

**Isn't there just a list of the skills in the right order?**

This skill is that list. A static sequence would be wrong for most situations, because the useful parts are the branches: is there a repo, does the build span sessions, can talking settle this question. Ask for "just the sequence" and you get it; the prose carries where to clear and where you decide.

**It says a skill isn't installed.**

Most of the skills it routes to set `disable-model-invocation: true`, so the harness leaves them out of the skill list the agent sees, and the agent can read that list as exhaustive. They are there. Type the slash command anyway.

**It described a skill's behaviour, and the skill doesn't do that.**

The router is told to read a skill's `SKILL.md` before stating what it does or recommending a step be skipped. If it answered from its own one-line summary instead, ask it to open that `SKILL.md`. Where the two disagree, the `SKILL.md` is right.

## It's working if

- It ends by naming what to type and stops there.
- The route mentions where to clear or compact context and where you are expected to decide, not just skill names.
- Where two skills are close, it says which one and why the other is wrong for you.
- Claims about another skill's behaviour show up in the trace as it reading that skill's `SKILL.md`.

## Where it fits

`ask` is a **standalone router** over the whole set, never a step in a chain. You most often land from here on [grill-with-docs](grill-with-docs.md), the head of the main flow, or [diagnosing-bugs](diagnosing-bugs.md) when something is broken. Every other docs page links back here so none of them has to redraw the map.
