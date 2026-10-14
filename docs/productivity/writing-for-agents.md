## What it does

`writing-for-agents` is the reference for writing any document an agent consumes: a skill, an `AGENTS.md` or `CLAUDE.md`, or a doc an agent reaches through a pointer. The packaging differs, but the writing does not. The same levers make each one predictable, so the agent takes the same *process* on every run (not necessarily to the same output).

Its default fix is to delete, not to explain. Ask an agent to write instructions for another agent and it spends most of its words explaining what the model already knows. Each such line is a **no-op**: it costs context and changes no behaviour. The reference helps you find them, so it is as useful on a document you already have as on a blank file. The skill-only mechanics (frontmatter, model- versus user-invoked, router skills) live in a linked `SKILL-MECHANICS.md`, read only when the document is a skill.

## When to reach for it

Type `/writing-for-agents`, or the agent reaches for it automatically when you create or edit a skill, or modify `AGENTS.md` or `CLAUDE.md`.

Reach for it by hand for anything else an agent reads: a spec or ticket under `plans/`, a system prompt for an evaluation harness, a doc your `CLAUDE.md` points at. The test is one question: does an agent read this? It controls how a document reads, not what it knows; to work out *what* belongs in a project's language, use [grill-with-docs](../engineering/grill-with-docs.md).

## The two loads

Every document and pointer spends one of two budgets:

- **Context load**: the cost of always-loaded material on the agent's window (an `AGENTS.md` line, a skill description), paid every turn whether or not it fires.
- **Cognitive load**: the cost to you of remembering which documents exist and when to reach for each. You are the index. This is the price of human agency, so spend it where your judgement matters.

Most authoring decisions (split or don't, inline or disclose) are the same trade between these two, made in different places.

## The levers

- **Context pointers**: the wording that names out-of-context material and when to reach it. The pointer's *wording*, not its target, decides how reliably the agent follows it.
- **Information hierarchy**: the ladder from in-file step, to in-file reference, to disclosed reference behind a pointer. **Progressive disclosure** moves material down it so the top stays legible.
- **Completion criteria**: how clear and demanding each step's done-condition is, the defence against **premature completion**.
- **Leading words**: a compact concept already in the model's pretraining (*tight*, *red*, *tracer bullet*) that anchors behaviour in the fewest tokens.
- **Pruning**: single source of truth, relevance, and the no-op test applied sentence by sentence, against **duplication**, **sediment**, and **sprawl**.

## Common questions

**Can't I just ask the agent to write it for me?**
You can, and it will come out verbose. Left alone the model explains what it already knows and won't apply the no-op test or reach for a leading word. Use the reference to review the draft; most of its value is in that pass.

**I asked an agent to trim a document and it cut the functionality.**
Agents told to "streamline" optimise for length, because length is what they can see. The no-op test checks behaviour: delete the line and ask whether the agent's behaviour changed. Settle disagreements by running the document, not by arguing.

**Should this live in `CLAUDE.md` or somewhere else?**
Ask which load you want to pay. `CLAUDE.md` loads into every session; material behind a pointer costs only the pointer's line until it fires. Something that matters in one session out of ten costs context load in the other nine.

**My skill only works on the exact task I built it from.**
Doing the work once and having the agent write it up fits that one run too closely. Keep the run as evidence, then generalise on purpose: strip what belonged to that repo and those files, and write for the class of task.

## It's working if

- The document gets shorter as it gets better.
- You can point at a leading word and see it change the agent's behaviour in more than one place.
- Nothing is stated twice, in any form.
- Reference that only one branch needs sits behind a pointer rather than in the main file.

## Where it fits

`writing-for-agents` is a **reach-for-it-anytime standalone** reference that applies to the whole set rather than one skill, including the documents other skills produce (a `GLOSSARY.md`, an ADR, a spec, a ticket). Its one direct caller is [retro](../engineering/retro.md), which loads it before proposing any steering file or skill. For the map of the whole set, see [ask](../engineering/ask.md).
