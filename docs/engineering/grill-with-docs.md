## What it does

`grill-with-docs` interviews you about a research plan or design until you and the agent share one understanding of it: the research question, the hypothesis, the data and splits, the baselines, the metrics, the compute. It is the same round-by-round interview as [grill-me](../productivity/grill-me.md), pointed at a repo.

It is **stateful**. Other grilling leaves the session in your head; this one leaves files on disk. When a term resolves, it goes into `GLOSSARY.md` at once, not in a batch at the end. When a decision is hard to reverse, surprising without context and a real trade-off, it becomes an ADR.

## When to reach for it

You invoke this by typing `/grill-with-docs`, and the agent won't reach for it on its own.

Reach for it at the start of a change, in a repo, while the plan is fuzzy and the words are not settled.

| What you have | Reach for |
| --- | --- |
| No repo under the idea (an experiment design you're sketching, a paper outline) | [grill-me](../productivity/grill-me.md) |
| A repo and a change to plan | `grill-with-docs` |
| A repo with no domain docs, and no particular change in mind | `grill-with-docs`, aimed at the repo ("help me document this repo") |
| A question that needs runnable code to settle | detour through [prototype](prototype.md), then fold the verdict back in |

## Prerequisites

It writes into your repo: terms to `GLOSSARY.md` at the root (or to a context's own `GLOSSARY.md` when a `GLOSSARY-MAP.md` marks the repo as multi-context), decisions to `docs/adr/`. Both are created lazily, so nothing needs setting up. Its `SKILL.md` delegates entirely to [grilling](../productivity/grilling.md) for the interview and [domain-modeling](domain-modeling.md) for the writing, so both must be present.

## The paper trail

| What resolved | Where it lands |
| --- | --- |
| A term: the project's word for a thing ("episode", "fold", "step") | `GLOSSARY.md`, the moment it resolves |
| A decision that is hard to reverse, surprising, and a real trade-off (an evaluation protocol, a split strategy) | An ADR under `docs/adr/` |
| Everything else you decided | The conversation, and nowhere else |

The third row catches people out. `GLOSSARY.md` is only a glossary, and most decisions do not earn an ADR, so most of what you agreed exists only in this context window. Hand the same conversation to [to-spec](to-spec.md) (or straight to [implement](implement.md) for a small change) rather than clearing it.

## Common questions

**It ran, but no `GLOSSARY.md` and no ADRs appeared.**

Usually nothing qualified: a session with no new vocabulary and no three-gate decision has nothing to write. A sharper understanding and zero ADRs is working as designed.

**It asked everything at once, with no recommendations, and never mentioned `GLOSSARY.md`.**

The agent failed to load its two dependencies and guessed at what grilling means. If only `grilling` loads you get a good interview with no paper trail. Ask the agent which skills it loaded.

**What do I do when the session ends?**

In the main flow, [to-spec](to-spec.md) in the same conversation. If the change fits one session, go straight to [implement](implement.md).

## It's working if

- `GLOSSARY.md` changes during the session, term by term, not in one lump at the end.
- The glossary holds pure vocabulary, no implementation detail or spec prose.
- Questions the codebase can answer get answered by reading it, not asked of you.
- It challenges a word you used because the glossary already defines it differently (an optimiser step versus an environment step).
- ADRs are few, and each is a decision you would hate to argue again.

## Where it fits

`grill-with-docs` is the head **chain step** of the main flow (`grill-with-docs → to-spec → to-tickets → implement → code-review`). It produces the shared understanding and vocabulary that [to-spec](to-spec.md) then synthesises without interviewing you again. Its neighbours are [grill-me](../productivity/grill-me.md), the same interview with no repo and no files, and [domain-modeling](domain-modeling.md), the glossary-and-ADR discipline it drives. When unsure which flow fits, [ask](ask.md) routes you.
