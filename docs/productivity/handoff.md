## What it does

`handoff` compacts the conversation you are in into a **handoff document**: one markdown file, written to your OS's temporary directory rather than the workspace, that a fresh agent can read to pick the work up.

What it gives you is **portability**, not compression, so the skill is narrower than it sounds. You need a file only when the work has to *travel*. If nothing is travelling, staying put, `/clear`, a subagent, or `/compact` covers the end of a phase, and `/compact` covers it more often than this skill does.

## When to reach for it

You invoke this by typing `/handoff`, and the agent won't reach for it on its own. Pass a note about what the next session is for, and the document is tailored to that.

There are four triggers:

| Situation | Why a file |
| --- | --- |
| Swapping harness | The new harness cannot see the old context |
| Moving to a different directory | A prototype folder or a cluster checkout is the common case |
| Sending the work to a collaborator | They need something they can read |
| Forking a side task found mid-phase | You keep working; a second agent takes the fork |

For anything else (same harness, same directory, done grilling and moving to implementation), use `/compact`. [ask](../engineering/ask.md) has the ordered tree over all five options at a phase boundary.

## Branching is the use people skip

Read as session resumption, `handoff` looks like a worse `/compact`. The fork case is the one worth knowing: you **stay in your session** and hand a copy of the accumulated context to a second agent working in parallel.

The detour through [prototype](../engineering/prototype.md) is the classic case. Deep in a design conversation, you hit a question only running code will settle. Hand off to a prototype session, get the verdict, bring it back. Your original thread stays live and you re-explain nothing.

## What travels, and what doesn't

The document carries the live thread (what is in flight, why, what's next) plus a **suggested skills** section naming what the next agent should reach for. Secrets and personal data are redacted.

It deliberately does not copy anything already written down. Specs and tickets under `plans/`, ADRs, research notes, commits, and diffs are referenced by path, which keeps the file small and the settled detail in one place.

## Common questions

**Handoff or compact?**
`/compact` unless something is travelling. `/handoff` does not summarise better; its advantage is a file you can carry somewhere `/compact` can't reach.

**Where did my handoff file go?**
The temp directory (`$TMPDIR`, else `/tmp`; `%TEMP%` on Windows). Ask for the path and keep it. Some environments clear temp between sessions and a reboot empties it, so if the next session isn't starting soon, copy the file somewhere durable.

**How do I hand it to the next agent?**
Open the fresh session and point it at the path: read this file, then continue. Avoid pasting the summary into a shell command, where backticks or `$(...)` can silently truncate it.

**It captures the what, not the why.**
Pass the argument so the skill keeps the reasoning that bears on the next task. Then read the document before handing it over and downgrade any claim the session never verified ("the eval script is done"), since the next agent will trust it as fact.

## It's working if

- The document is a small fraction of the conversation, and specs, tickets, and diffs appear as paths rather than copied text.
- You can read it cold and know what to do next.
- The fresh agent starts working instead of asking you to re-explain the setup.
- In the fork case, your original session is unchanged when you return.
- Nothing in it is a key, a token, or a password.

## Where it fits

`handoff` is a **reach-for-it-anytime standalone** that works between sessions rather than inside the build chain, and the narrowest of the five phase-boundary options. Its closest neighbour is [prototype](../engineering/prototype.md), because a prototype lives in its own directory and the round trip out and back is what this skill is for. For the phase-boundary tree and the map of the whole set, see [ask](../engineering/ask.md).
