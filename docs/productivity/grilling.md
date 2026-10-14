## What it does

`grilling` is the interview loop that stress-tests a plan, a decision, or an idea before anyone acts on it. It maps the subject as a **design tree**: every decision branches into the decisions that hang off it, and it interviews you branch by branch until no assumption stays unstated.

It does not ask one question at a time, and it does not ask everything at once. Each **round** asks the whole **frontier**: every decision whose prerequisites are already settled, and nothing else. A question that hinges on an answer still open belongs to a later round. Your answers settle decisions, the frontier moves outward, and the next round asks what that unblocked. Thirteen questions typically land in about three rounds rather than thirteen.

## When to reach for it

Type `/grilling`, or the agent reaches for it automatically when a task fits. It is the only model-invoked skill in the grilling family, so you rarely type it: usually a skill you *did* type runs it for you.

Typing `/grilling` directly gets you the plain interview and nothing else. Where you want more than that:

| What you have | Reach for |
| --- | --- |
| No repo under the idea (an experiment design, a paper outline) | [grill-me](grill-me.md): the same interview, under a name the agent never fires by itself |
| A repo to align against | [grill-with-docs](../engineering/grill-with-docs.md): the same interview, and it writes `GLOSSARY.md` and ADRs as it goes |
| A question talking cannot settle (does this idea learn at all on a toy problem?) | [prototype](../engineering/prototype.md): build the throwaway version, then come back |
| A skill of your own that needs an interview | Invoke `/grilling` from it, rather than writing another interview |

## The round, the frontier, and who decides

The **design tree** is the model of the subject. The **frontier** is the set of decisions whose prerequisites are all settled: the only questions the agent can ask yet. A **round** is one frontier, asked in full and answered in full.

Every question in a round is numbered and titled behind a `❓`, followed by the agent's recommended answer on its own `➡️` line, and worded so that "yes" accepts the recommendation. You can answer a whole round by number ("1 yes, 2 the second option, 3 no, here's why").

Facts are the agent's job; decisions are yours. When a frontier question needs something the environment can settle (what the config defaults to, how the dataset is split today), the agent dispatches a sub-agent to look it up rather than asking you. Only the questions downstream of that lookup wait; the rest of the round goes ahead. Decisions it puts to you and waits for. The session ends when the frontier is empty, and the agent then waits for you to confirm a shared understanding before it acts.

The frontier is chosen by judgement, not computed from a graph, so the agent can occasionally pair two questions in one round and only later find one answer should have changed the other. Tell it, and it reopens that branch next round.

## Common questions

**Can I go back to one question at a time?**
Yes. Add this to your global `CLAUDE.md`:

```
When grilling, ask one question at a time.
```

Some people focus better that way, and it is a supported setting, not a workaround.

**Doesn't asking a whole round at once lose the questions my earlier answers would have raised?**
No, because a round only holds questions that do not depend on each other. Your answers still shape everything after them, since the agent recomputes each next round from scratch.

**It ran out of questions and started building.**
The skill is finished when you confirm the understanding is shared, not when the frontier empties. Weaker or lower-effort models sometimes skip that gate. If yours does, add a line to your `CLAUDE.md` telling the agent not to implement without permission.

**Can I cap the number of questions?**
No. Some plans need three questions and some need fifty. Tell it to wrap up in plain language, or, if a session runs very long, break the scope into smaller pieces and grill each one.

## It's working if

- A round arrives as a numbered list, each question with its recommendation on a separate `➡️` line, and you can answer the whole round by number.
- Nothing in a round needs another question in the same round answered first.
- Later rounds ask things the first round could not have asked.
- It looks facts up (reading files, dispatching a sub-agent) rather than asking you something it could have found out.
- It stops at the end and asks you to confirm the understanding is shared, instead of starting work.

## Where it fits

`grilling` is a **primitive**, not a step you schedule: the single source of truth for the interview, so every skill that needs one reaches for it instead of inventing its own. [grill-me](grill-me.md) and [grill-with-docs](../engineering/grill-with-docs.md) are its two user-invoked front doors, and `grill-with-docs` is where the main build chain begins, ahead of [to-spec](../engineering/to-spec.md). [improve-codebase-architecture](../engineering/improve-codebase-architecture.md) runs it to walk the tree once you have picked a candidate to deepen. For the map of the whole set, see [ask](../engineering/ask.md).
