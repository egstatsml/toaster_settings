## What it does

`research` answers a question by reading the sources that own the answer, then leaves a cited Markdown file in the repo. It works only from **primary sources**: the paper itself (including the appendix), the authors' reference implementation, the official docs and source of a library at the version this repo pins, a dataset's datasheet, loader, and licence. It will not repeat a blog post's account of a method when the paper is reachable.

It does not answer you in the conversation. The output is a file with a citation on every claim (paper section, equation, or table; file and line for code), recording the exact hyperparameters, splits, and reported numbers that reproduction needs. Where the paper and the code disagree on a detail (a normalisation, a schedule, a loss term), it reports both.

## When to reach for it

Type `/research`, or the agent reaches for it automatically when a task turns into reading legwork: a method or paper to understand, related work or baselines to gather, implementation details to check against a paper.

| What you need | Reach for |
| --- | --- |
| A fact from outside the repo that a decision is waiting on | `research` |
| A decision made *with* you, by interview | [grilling](../productivity/grilling.md) |
| A durable decision written into `GLOSSARY.md` and ADRs | [grill-with-docs](grill-with-docs.md) |
| To find out whether an idea actually learns, or what the data looks like | [prototype](prototype.md) |

The line between `research` and `grill-with-docs` is the shelf life of what comes back. Research produces facts (what the paper's warmup schedule is, what this library version's default does); an ADR records a decision you keep.

## Prerequisites

A place for the file. It goes where the repo already keeps such notes; with no convention, next to the plan it feeds at `plans/<slug>/research/<topic>.md`, or `docs/research/<topic>.md` when there is no plan. It tells you the path.

## Delegated legwork

The reading runs as a **background agent**, so you keep working while it follows each claim to its source. Research is legwork you delegate, not thinking you outsource: you get a document to grill, plan, or design against, and you still make the decision.

## Common questions

**It spawned a second research agent. Is that meant to happen?**

No. The skill tells its caller to start a background agent but does not restrict what that agent can do, so a general-purpose agent can read the same instructions and delegate again. After invoking it, watch your background tasks and stop a duplicate. The opposite also happens: if your own instructions forbid sub-agents from re-delegating, the background agent may decline the task without telling you.

**What counts as a primary source, and who checks?**

The skill names the kinds of source that qualify, but there is no allowlist and no verification pass. Your protection is the citation on each claim: follow two or three. If they land on a summary or a blog post rather than the paper or the code, the run failed.

**When does it stop reading?**

The skill has no stopping criterion, so a broad question tends to come back either too deep or broad but missing the one detail that mattered. Scope it yourself: "what learning-rate schedule and warmup does the reference implementation actually use" comes back far better than "research this paper".

**Does a later session reuse what it found?**

Not automatically. The file is useful when you feed it into the next step yourself: take it into [grill-with-docs](grill-with-docs.md), or point the spec or a ticket at it.

## It's working if

- Your own session keeps going while it reads.
- Exactly one new background task appears.
- One new Markdown file shows up in the expected folder, and the agent tells you the path.
- Every claim carries a citation, and following two at random lands you on a paper section, a table, or a line of the reference code.
- Paper-versus-code disagreements are called out rather than silently resolved.
- You can make the decision you were stuck on from the file alone.

## Where it fits

`research` is an on-ramp, not a step in the build chain: you take its file *into* the main flow at [grill-with-docs](grill-with-docs.md), which asks sharper questions when the facts are already in hand, and [to-spec](to-spec.md) can synthesise against it. [prototype](prototype.md) is the sibling for questions only running code can answer. For the whole map, see [ask](ask.md).
