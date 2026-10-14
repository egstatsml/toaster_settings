# Writing docs pages

Every skill has a human-facing **docs page** at `docs/<bucket>/<skill-name>.md`. The docs tree mirrors the bucket folders under `skills/`. The page is not the skill and not a copy of `SKILL.md`.

Most of these skills are **user-invoked**: the agent will never fire them for you, so *you* are the index that has to remember they exist and when to reach for them. The job of a docs page is to orient one reader around one skill so they can hold it in their head, know when to reach for it, and see where it sits in the system.

Act whenever a skill is added, renamed, or has its behaviour changed: create or re-sync its docs page. A rename moves the file too (`docs/<bucket>/<old>.md` → `docs/<bucket>/<new>.md`); a skill that moves between buckets moves its docs file to the matching folder. A removed skill loses its page.

Links are **relative**, so they work when browsing the repo: a link to another skill's page is `../<bucket>/<name>.md` (or `<name>.md` within the same bucket); a link to a skill's source is `../../skills/<bucket>/<name>/SKILL.md`.

There is no H1. The page takes its title from the filename.

## Page structure

Fill the template below, keeping its order. The **fixed frame** (`## What it does`, `## When to reach for it`, `## Where it fits`) appears on every page. `## Prerequisites` and the free-form substance sections carry only what this particular skill needs; delete the rest.

Four sections make a page worth reading: `What it does`, `When to reach for it`, `Common questions`, `It's working if`. The first two orient the reader; the last two answer the reader's own situation.

<page-template>

## What it does

One or two plain-language paragraphs. Lead with the skill's one-sentence job, then state the **defining constraint**: the single fact that makes this skill behave differently from the obvious default (for `to-spec`: it does not interview the user again, it synthesises what is already known). Write it as a plain declarative sentence, never a labelled aside like "The defining constraint:"; the formula reads as filler.

## When to reach for it

How and when you reach for the skill, in two beats:

- **Invocation mode.** A user-invoked skill: "You invoke this by typing `/<name>`, and the agent won't reach for it on its own." A model-invoked skill: "Type `/<name>`, or the agent reaches for it automatically when a task fits."
- **Trigger boundary.** "Reach for this when …". Where the skill is confusable with a sibling, add the other half: "for <X> instead, use [<sibling>](<sibling>.md)."

## Prerequisites

Optional: include only when the skill needs something in place: a **workspace it writes into** (`grill-with-docs` writes `GLOSSARY.md` and ADRs; `to-spec` and `to-tickets` write under `plans/<slug>/`), or **repo-specific tooling**. A stateless skill has no prerequisites, so drop the section.

## <free-form middle>

One to three short sections, in the skill's *own vocabulary*, that make it click: the loop it runs, the artifact it produces, the fork it makes, the one anti-pattern it kills. Surface the skill's leading word or defining idea (_tight_ feedback loop, _deep module_, throwaway-code-answers-a-question, red-green).

## Common questions

The questions a researcher would plainly ask about this skill, each in bold with the answer in the lines beneath it. No sub-headings. Keep the count honest: one or two real questions beat six invented ones. Omit the heading where there is nothing worth answering.

## It's working if

A few bullets naming what the reader sees when the skill is doing its job, each checkable without opening `SKILL.md`. Omit the heading where the tells stay vague.

## Where it fits

Always present. Situate the skill in a sentence or two:

- **Role.** A **chain step** (`grill-with-docs → to-spec → to-tickets → implement → code-review`), **periodic maintenance** (`improve-codebase-architecture`), or a **reach-for-it-anytime standalone** (`diagnosing-bugs`, `prototype`, `handoff`).
- **Neighbours.** The one or two siblings that matter, each with a because-clause, linked.
- **The map.** Point to [ask](../engineering/ask.md), the router over the whole set.

</page-template>

## Conventions

- Explain the **why**, not the process. Never reproduce the `SKILL.md` steps or templates.
- Use the skill's **leading words** (_seam_, _deep module_, _tracer bullet_) so the page and the skill speak one language.
- **Branches go in a table or a list, never in a paragraph.**
- Keep the page low-load: no spare headings or restated links.
- No install commands, no references to an issue tracker, pull requests, or external publishing.

## Done when

- The page exists at `docs/<bucket>/<name>.md`, and no stale page survives a rename, move, or removal.
- `## What it does` states the defining constraint as plain prose.
- `## When to reach for it` states invocation mode and the trigger boundary.
- `## Where it fits` names the role and links to `ask`.
- The middle surfaces the leading word.
- Every multi-way branch is a table or a list.
- Every link is relative and resolves.
