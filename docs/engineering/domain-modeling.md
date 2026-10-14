## What it does

`domain-modeling` builds and sharpens a project's **domain language** while you design. It challenges a term that conflicts with the glossary, asks for a precise word where you used a vague one ("step": optimiser step or environment step?), and tests a relationship against a concrete scenario until the boundaries are exact.

It is the **active** discipline, not the passive one. Any skill can read `GLOSSARY.md` to borrow its vocabulary; this skill is for when you are *changing* the model, which is why it interrupts you. It writes a term into `GLOSSARY.md` the moment you settle it, mid-conversation, rather than producing a tidy glossary at the end.

## When to reach for it

Type `/domain-modeling`, or the agent reaches for it automatically when a task fits. Automatic invocation is the weak spot: when [grill-with-docs](grill-with-docs.md) says to load it, a model may load only `grilling`. If a grilling session ends with `GLOSSARY.md` unchanged, invoke this by name alongside it.

Reach for it when the *words* are the problem:

| The situation | The move |
| --- | --- |
| "Validation set" means the tuning split in one script and the final held-out split in another | `domain-modeling`: pick the canonical term, list the other under `_Avoid_` |
| "Sample" is doing three jobs: a dataset row, a draw from the model, a rollout | `domain-modeling`: split it into precise terms |
| You just fixed an evaluation protocol or a split strategy | `domain-modeling`: it offers an ADR, if the choice clears the bar |
| The module's *shape* is the problem: where the seam goes, how deep the interface is | [codebase-design](codebase-design.md) |
| You want the whole plan interrogated before you build | [grill-with-docs](grill-with-docs.md), which drives this skill underneath |
| You want a term looked up, not changed | Nothing. Read `GLOSSARY.md`. |

## Prerequisites

None up front. It writes into two places and creates both lazily:

- **`GLOSSARY.md`** at the repo root, created when the first term is settled. In a repo with a `GLOSSARY-MAP.md`, terms go into the per-context `GLOSSARY.md` the map points at (for example separate data, modelling and evaluation contexts).
- **`docs/adr/`**, created with the first ADR that clears the bar.

## Two artifacts, two bars

The glossary and the ADR have different bars, and most trouble comes from mixing them up.

| | `GLOSSARY.md` | `docs/adr/NNNN-slug.md` |
| --- | --- | --- |
| Holds | What a thing **is**, in one or two sentences, with rejected synonyms under `_Avoid_` | One decision in one to three sentences: context, choice, reason |
| Bar to write | A vague term became canonical | **All three**: hard to reverse, surprising without context, the result of a real trade-off |
| Written | Inline, the moment the term is settled | Offered, not assumed |
| Never holds | Implementation details, a spec, general ML concepts (learning rate, batch size) | A diary of every choice made this session |

ADR-worthy decisions in research code are things like "splits are grouped by subject, so no subject appears in both train and test" or "we report mean and standard deviation over five seeds": choices that decide whether results stay comparable over time.

**`GLOSSARY.md` is a glossary and nothing else.** Left unchecked, models treat "write to `GLOSSARY.md`" as permission to persist every answer you give, and the file turns into a running spec.

## Cross-referencing the code

When you state how something works, the skill checks the code and points out any contradiction: *"Your code normalises with statistics from the whole dataset, but you just said only the training split is used. Which is right?"* It makes the language and the code agree, out loud, before changing either. In research code this is often where leakage first surfaces.

## Common questions

**My `GLOSSARY.md` has grown to hundreds of lines. What do I do?**
The size is a symptom: the file has taken in implementation detail and decisions that never belonged in a glossary. Ask directly: `/grill-with-docs make my GLOSSARY.md more concise and remove any implementation details from it`. Only split it with a `GLOSSARY-MAP.md` once it is lean and still covers areas a reader wouldn't want to hold at once; splitting a bloated file gives you several bloated files.

**How do I get a glossary for a codebase that has none?**
Ask for it rather than waiting for it to accumulate: `/grill-with-docs help me scaffold my existing repo with a GLOSSARY.md`. Expect a long interrogation.

**Does a glossary earn its keep on a short project?**
Not always. The payoff is in naming and concept alignment (module names, config keys, metric names, what a "split" or an "episode" is), and in keeping you aligned with what the agent is doing. On a one-day experiment, skip it. An unreviewed, agent-authored glossary is worse than none: later sessions treat its confident claims as true.

## It's working if

- It stops you mid-sentence to ask which of two things you meant, instead of picking one and moving on.
- `GLOSSARY.md` changes **during** the conversation, not in a burst at the end.
- It refuses to write an ADR for something you could undo tomorrow, and says which test failed.
- New entries define what a thing *is* and name the words you're giving up under `_Avoid_`.
- It quotes your code back at you when your code and your sentence disagree.

## Where it fits

`domain-modeling` is a **model-invoked reference** in the vocabulary layer, running *underneath* other skills more often than on its own: [grill-with-docs](grill-with-docs.md) drives it through an interview, and [improve-codebase-architecture](improve-codebase-architecture.md) calls it as decisions settle. Its sibling is [codebase-design](codebase-design.md): this one owns the *domain's* words, that one the module's *shape*. It is also reachable directly when you want the discipline without a driver skill. When you're unsure which skill fits, [ask](ask.md) routes you.
