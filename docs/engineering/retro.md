## What it does

`retro` looks back over a coding session and suggests improvements to the agent's **environment**, so the next run goes better. It reads the session's own record (the current one by default, or one you point it at in the session logs), finds the moments where the agent struggled, and gives you a list of candidate fixes, most severe first.

It changes the environment, not the code. Take the leakage bug the agent shipped, the config file it needed twenty tool calls to find, or the rule the reviewer missed: `retro` fixes none of them directly. It asks what in the repo let them happen, and proposes the check, pointer, or standard that prevents them next time. It only proposes; nothing changes until you pick a candidate.

## When to reach for it

You invoke this by typing `/retro`, and the agent won't reach for it on its own.

Reach for it at the end of a session that was harder than it should have been: the agent searched too long, made a mistake a tool could have caught, or needed information it could not get (the training log, the job status, a fixture dataset). A smooth session has little to teach. For a verdict on the code the session produced, use [code-review](code-review.md) instead.

## Where the findings land

Each candidate belongs to one category, and the category decides where the fix goes:

| What went wrong in the session | Fix it with |
| --- | --- |
| The agent took a long time to find a file or fact | A **navigation pointer** from a file it already reads |
| It made a mistake a tool could have caught | An **automated check**: lint rule, type check, test, pre-commit hook, CI job |
| The reviewer missed a judgement-call mistake | A rule in `CODING_STANDARDS.md` for the reviewer agent |
| `AGENTS.md` or `CLAUDE.md` is large | Move its steering out, into standards or checks |
| A tool call was expensive for what it returned | Streamline the tool, or replace it |
| A steering file is full of lines that change nothing | Delete the **no-ops** |
| The agent needed information it couldn't reach | Widen its access: tee training and evaluation logs to a file, read-only access to the experiment tracker or cluster job status, a small fixture dataset |

The leading idea is that standards belong to the **reviewer**, not the implementer. The implementing agent carries the most context pressure (it explores, writes code, debugs); the reviewer gets a diff and nothing else. So a new rule goes where there is room to apply it, which is review, never into `AGENTS.md`, which loads into every session whether it is relevant or not.

Before writing any rule, `retro` classifies the violation. A **mechanical** one (a banned API, an import shape, a file-location rule) gets a deterministic check, because a check can fail and a sentence cannot. Only real judgement calls become prose. It reads the repo's own check command first (`pyproject.toml`, `Makefile`, pre-commit config, CI workflow), so a check that exists but sits unwired is the finding, not a reinvention. A repo with no **guardrail** at all is a finding of its own.

## Common questions

**Does it write the check itself, or wait for a yes?**

It waits. Deciding what deserves a permanent check takes judgement, so the skill stays user-invoked and installs nothing on its own. A proposed check is code like any other: try it against the repo before you let it block anything.

**Won't it just invent generic advice to fill its categories?**

That is its main risk. Every candidate must come from the session's own record, so discard any you can't trace to a specific moment. The flip side is that it can over-weight whatever this one session was about, so treat the severity order as a first draft.

**My session is long. Run it now, or start fresh?**

By default it reviews the current session, which is the best case because the struggles are still in context. If the session has drifted past the smart zone, clear it and point a fresh `/retro` at the previous session's log.

**The agent keeps making the same mistake. Should I add a line to `CLAUDE.md`?**

Usually not, and `retro` pushes back on this more than anything else. A line there loads into every session, dilutes everything else in the file, and goes stale. A mechanical mistake wants a check that fails; a judgement call goes in the standards the reviewer reads. `retro` is not a memory system: it changes the environment so the mistake cannot recur.

## It's working if

- Every candidate points back to a specific moment in the session, not a generic best practice.
- Repeat mistakes turn into failing checks, and your `AGENTS.md` gets shorter over time, not longer.
- If a check already existed but was not wired up, the finding is to wire it, not to build a new one.
- The next session on the same kind of task finds what it needs faster.

## Where it fits

`retro` is the last step of the main chain, `grill-with-docs → to-spec → to-tickets → implement → code-review → retro`, and the natural follow-up to [diagnosing-bugs](diagnosing-bugs.md), asking what would have prevented the bug. Run it in the same session or point it at that session's log; skip it after a smooth build.

- [code-review](code-review.md) is the reviewer agent `retro` most often tunes: new coding standards go where its Standards axis reads them.
- [writing-for-agents](../productivity/writing-for-agents.md) sets the style for every steering file and skill `retro` proposes, and `retro` loads it before it starts.

[ask](ask.md) routes across the whole set when you are unsure which skill the situation needs.
