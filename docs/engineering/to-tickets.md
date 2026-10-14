## What it does

`to-tickets` breaks a spec, a plan, or the conversation you are in into a set of **tickets**, one markdown file each under `plans/<slug>/tickets/`. Each ticket declares its **blocking edges**: the tickets that must finish before it can start.

Every ticket is a **tracer bullet**: a narrow but complete path through every layer it touches (config, data, model, training or evaluation loop, logging, tests), runnable on its own as a short run on a tiny fixture. The obvious alternative, one layer per ticket with integration at the end, is what it refuses. Each ticket is also sized to fit one fresh context window, because a session that never saw your spec will pick it up.

## When to reach for it

You invoke this by typing `/to-tickets`, and the agent won't reach for it on its own.

| Where you are | What to run |
| --- | --- |
| A spec, and the build spans several sessions | `/to-tickets plans/<slug>/spec.md`, or just `/to-tickets` in the same window |
| The plan lives only in the conversation | `/to-tickets` reads the thread directly |
| The whole change fits one context window | [implement](implement.md), skip the tickets |
| Nothing decided yet | [grill-with-docs](grill-with-docs.md), then [to-spec](to-spec.md) |

## Prerequisites

It writes into the repo you are working on, next to the spec in `plans/<slug>/`, or under a new slug when there is no spec.

## Tracer bullets, not layers

A horizontal slice (all the data loading, then all the model, then the training loop) makes nothing runnable until every slice has landed, and each ticket's acceptance criteria reach into work another ticket owns. A vertical slice owns everything it grades. The typical first tracer bullet for a new experiment: load a handful of examples, push them through a minimal model, compute the loss, take one optimiser step, log one metric. Later tickets widen each layer.

Before writing anything it orders prefactoring first ("make the change easy, then make the easy change"), then shows the breakdown as a numbered list and quizzes you on granularity and blocking edges. Nothing is written until you approve; that quiz is where to push back.

A ticket delivers code verified on small inputs. Launching a full training run or a sweep is your call, never a ticket's acceptance criterion.

## Blocking edges and the frontier

Files are numbered `01`, `02`, ... in dependency order, blockers first, each with a `Blocked by:` line and a `Status:` that moves `todo` → `in-progress` → `done`. The **frontier** is every ticket whose blockers are all `done`; for a linear chain that is simply top to bottom.

**Wide refactors** are the exception to vertical slicing: one mechanical change (rename a config key, change a tensor layout used everywhere) whose blast radius no single slice can land green. Those are sequenced as **expand-contract**: add the new form beside the old, migrate call sites in batches (one ticket each, blocked by the expand), then delete the old form in a ticket blocked by every batch.

## Common questions

**It produced twelve tickets for a small change.**

Over-decomposition is common. Ask it to merge at the quiz step. If the whole change fits one context window, you don't need this skill: go to [implement](implement.md).

**The tickets came out one per layer.**

Catch it at the quiz by asking, per ticket, what you can run when it is done. A ticket with no runnable answer is a horizontal slice.

**Some acceptance criteria were already true before any work.**

Vertical slicing prevents most of this, since a slice delivering new behaviour fails at the start by construction. Still check: for each criterion, name the observation that would show it false.

## It's working if

- The numbered breakdown comes back for approval, with a `Blocked by` on each, before any file is written.
- Every ticket answers "what can I run when this is done?" with behaviour, not a layer.
- `01` has no blockers and can start immediately, and prefactoring sits at the front.
- No ticket body contains file paths or line numbers, except a snippet a prototype produced.
- There is one file per ticket under `plans/<slug>/tickets/`, never one combined file.

## Where it fits

`to-tickets` is a **chain step** in the main flow (`grill-with-docs → to-spec → to-tickets → implement → code-review`). Upstream, [to-spec](to-spec.md) hands it a settled spec; keep both in one context window. Downstream, [implement](implement.md) builds one ticket per fresh session, driving [tdd](tdd.md) and closing with [code-review](code-review.md). When unsure which flow fits, [ask](ask.md) routes you.
