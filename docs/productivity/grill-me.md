## What it does

`grill-me` takes a **loose idea** and interviews you until you can commit to it. You do not need a worked-out plan to start, because the session exists to produce one. It asks in **rounds**, each round being the whole **frontier** (every question whose prerequisites you have already settled), so it never asks something that depends on an answer it hasn't heard yet.

It is **stateless**. It writes no files and leaves nothing behind. The only result is a clearer version of the idea, in your own head and in the conversation.

## When to reach for it

You invoke this by typing `/grill-me`, and the agent won't reach for it on its own. Start it in a fresh conversation, not on top of a plan you already had an agent write.

Reach for it as soon as you have an idea worth taking seriously (an experiment design, a paper outline, what to say to a collaborator about a result) and long before you have worked out what it involves. Vagueness is not a reason to wait.

- **No repo under the idea.** Use `grill-me`. The subject doesn't have to be code.
- **A repo to align against.** Use [grill-with-docs](../engineering/grill-with-docs.md) instead: the same interview, but stateful, reading your code and keeping what it learns in `GLOSSARY.md` and ADRs.

Leave plan mode off. It makes the agent hurry to a plan when you want it to keep asking.

## It's a conversation, not an interview

The skill asks the questions, but **you** own the scope. The failure mode is **passivity**: answering "agreed, agreed, agreed" for forty questions and coming out with a plan the agent wrote and you nodded at. It feels productive because it was long, but you decided nothing.

Being active means steering: push back on a question that is less detailed than you need, say when the scope is drifting, and answer "I don't know" when you mean it. The quality of the result depends on your answers, not on the number of questions.

## Grillable and ungrillable

Some questions can be answered by talking. Others can't. "Will this augmentation help on our data?" or "does the loss even go down on a toy version?" are **ungrillable**: you need something to react to. When you hit one, stop grilling, answer it with [prototype](../engineering/prototype.md), then come back and settle it in one line. Sessions balloon when you try to talk through an ungrillable question.

## Common questions

**How many questions should I expect, and how do I know when it ends?**
Count rounds, not questions. Dozens of questions across four rounds is an ordinary session. It ends when the frontier is empty and you confirm the understanding is shared.

**It asked me far too many questions. What went wrong?**
Usually the scope was too large. Ask the agent to break the work into smaller pieces first, then grill each one. Very long sessions also drift out of the smart zone, where a full context window makes the questions worse.

**Can I go back to one question at a time?**
Yes. Add `When grilling, ask one question at a time.` to your global `CLAUDE.md`.

**Do I start a fresh session before writing the spec?**
No. The value of the session is the context you just built. If the idea turns into code, carry the same conversation into [to-spec](../engineering/to-spec.md).

## It's working if

- You disagree with something. A session with no pushback from you is one you didn't need.
- Questions arrive in a few rounds, and later rounds clearly build on what you said earlier.
- You end up somewhere you didn't expect, because a question exposed a decision you had been making without noticing.
- At the end you could defend each choice to a collaborator who wasn't there.

## Where it fits

`grill-me` is a **reach-for-it-anytime standalone**. Because it is stateless it needs no repo or setup, and the idea need not be software. Portability is its only difference from [grill-with-docs](../engineering/grill-with-docs.md); both run [grilling](grilling.md) underneath. For the map of the whole set, see [ask](../engineering/ask.md).
