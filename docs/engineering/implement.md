## What it does

`implement` builds work that has already been decided. Point it at a ticket in `plans/<slug>/tickets/`, a spec, or the plan you just agreed in conversation, and it writes the code, drives [tdd](tdd.md) at the seams, runs the code path end to end on a tiny input, runs [code-review](code-review.md), updates the ticket and commits to the current branch.

It never reopens the plan: no interview, no clarifying round, no alternative approach. Whatever was settled upstream is the input. That separates it from typing "build this" at a fresh agent, which often redesigns the work while building it.

## When to reach for it

You invoke this by typing `/implement`, and the agent won't reach for it on its own (no other skill can call it either).

| The work is... | Reach for |
| --- | --- |
| A ticket file | `/implement 03` or the ticket path, one ticket per session, clearing context between tickets |
| A spec, not yet split, spanning sessions | [to-tickets](to-tickets.md) first |
| A small spec, or a small plan still in this conversation | `/implement` directly (say so if the plan lives only in the thread) |
| Not decided yet | [grill-with-docs](grill-with-docs.md) |
| One concrete behaviour you want test-first, no plan | [tdd](tdd.md) directly |
| Already built, and you want it checked | [code-review](code-review.md) directly |

## Prerequisites

It commits to the branch you are on without creating one or asking, so check your branch first.

## Small inputs, not real runs

A run ends at code that is shown to work, not at a result. Besides tests at the seams, it runs the real code path on a tiny input (a few batches, a few steps, on CPU where it can) so a shape mismatch or a crash in the training loop surfaces now rather than an hour into a cluster job. It never launches a full training run or a sweep unless you ask; that stays your call.

## Pre-agreed seams

Tests live at **seams**: the data pipeline's output, a forward pass, a loss or metric, a short run on a tiny fixture. When the seam is agreed before code exists, the tests survive rewrites underneath. Nothing inside `implement` agrees seams: that happens upstream in the spec, or in the first exchange of the run, and `tdd` refuses to test at an unconfirmed one. Naming seams in the spec is what keeps a run from turning into "just write the code".

## Common questions

**Can I point it at all my tickets at once?**

No: one invocation, one ticket, then clear and take the next ticket on the frontier. Running several sessions side by side in one checkout is unsafe, because they share one working directory, index and HEAD.

**One ticket used a huge amount of context. Am I using it wrong?**

Probably the ticket is too big. Exploration, a red-green loop per seam, a smoke run and a review add up. Split the ticket in [to-tickets](to-tickets.md) rather than pushing on.

**Should the review run inside the same session?**

It does by default. If you worry about an agent being soft on code it just wrote, run [code-review](code-review.md) again in a fresh session against the point you branched from.

## It's working if

- It opens by stating the ticket's title and what it will build, and sets the ticket to `in-progress`.
- You see an actual `tdd` Skill tool call in the trace, not just tests appearing in the diff.
- Single test files run repeatedly, the full suite once at the end, and a tiny end-to-end run happens before review.
- The ticket ends `done`, with its criteria ticked and a short `## Notes` entry, committed alongside the code.
- The diff is one ticket's worth of change, and no long training job was launched.

## Where it fits

`implement` is the build **chain step** of the main flow (`grill-with-docs → to-spec → to-tickets → implement → code-review`). [to-tickets](to-tickets.md) produces the tickets it consumes and their order; [tdd](tdd.md) runs inside it at each seam; [code-review](code-review.md) closes it out. It trusts what it was handed, so a horizontally sliced ticket gets built as written. When unsure which flow fits, [ask](ask.md) routes you.
