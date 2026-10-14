## What it does

`wait-what` makes the agent re-pitch something you didn't follow. It adds the context you were missing, writes in plain English (ASD-STE100 Simplified Technical English), and uses the vocabulary from your project's `GLOSSARY.md`.

The skill is one line long, and that is the design. Skills against verbosity usually fail by growing long themselves, and the model copies the length of the skill rather than its request. This one carries a single precise leading word and nothing else.

## When to reach for it

You invoke this by typing `/wait-what`, and the agent won't reach for it on its own. Only you know when you stopped following.

Reach for it as soon as you notice you're skimming: the agent has started using jargon it invented, stacked five acronyms in one sentence, or explained a decision whose premise you never saw. It fixes the conversation you're already in. To stop the jargon arriving at all, use [grill-with-docs](../engineering/grill-with-docs.md), which builds the shared language upfront.

## The name is the mechanism

The leading word is **wait**. "Be concise" is an instruction about the agent's output, and the model obeys it by clipping words, which loses you further. **Wait** is about *your* state: you stopped understanding here. An agent told "be brief" writes fragments; an agent told "wait, you lost me" backs up and explains. Naming the listener asks for both halves at once: fewer words **and** the missing context.

The skill says re-pitch **that**, not "that last message". What lost you is often bigger than one paragraph, so the agent decides how far back to go.

If the repo has no `GLOSSARY.md` (and no `GLOSSARY-MAP.md` pointing to one), the skill still works; you lose only the domain-vocabulary half.

## It's working if

- The re-pitch is **shorter and clearer**, not shorter and blunter.
- It adds the premise you were missing, instead of only deleting words.
- Project nouns replace invented ones: the terms in your `GLOSSARY.md` come back.
- You can use it twice in a row without it degrading into terseness.

## Where it fits

`wait-what` is a **reach-for-it-anytime standalone**, usable at any point, inside any other skill. It repairs one message after the fact; the lasting fix is a shared language agreed upfront, which is what [grill-with-docs](../engineering/grill-with-docs.md) records via [domain-modeling](../engineering/domain-modeling.md). For the map of the whole set, see [ask](../engineering/ask.md).
