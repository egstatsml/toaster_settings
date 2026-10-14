## What it does

`improve-codebase-architecture` surveys a codebase for **deepening opportunities**: places where a shallow module (an interface nearly as complex as the thing it hides) could become a deep one. It writes them up as a self-contained HTML report, then grills you through the one you pick.

It never changes the code. The whole run produces a conversation and one HTML file in your OS temp directory. You do the refactor later, in a separate session, through the normal build flow. That makes it a survey, not a refactoring tool, so you can run it on a codebase you are not ready to touch yet.

Two filters keep the report from becoming generic cleanup advice. Every candidate must pass the **deletion test**: if you removed this module, would its complexity concentrate behind a smaller interface, or just move to the callers? Only the first gets a card. And unless you point it at a specific area, it reads recent commit history first and focuses on the paths that keep changing. A deepening in code nobody touches never pays back.

## When to reach for it

You invoke this by typing `/improve-codebase-architecture`, and the agent won't reach for it on its own.

It is not a step in the main flow. You run it when you have a spare moment, to queue up work that keeps the codebase good to operate in. Research code accretes fast (copy-pasted training loops, a flag per experiment, scripts that import from scripts), so the typical situations are:

| Situation | How it is used |
| --- | --- |
| Routine upkeep | Run it every so often so structure doesn't decay between experiments. |
| Before a big build | Point it at the spec and ask "how can we make this change easy?" This is the most effective prompt for it. |
| Inherited or sprawling repo | Run it to find out what shape the code is in. |
| Untestable code | Use it to find the missing seams before writing tests. |

Where it is confusable with siblings:

- To design one module you have already chosen, use [codebase-design](codebase-design.md). This skill finds the module; `codebase-design` is where you design it.
- For "this specific thing is broken," use [diagnosing-bugs](diagnosing-bugs.md). It sends you back here when the real finding is that there is no good seam to lock the bug down.

## Prerequisites

None to run it. If `GLOSSARY.md` or ADRs in `docs/adr/` exist, it reads them and uses your domain's own nouns, so a candidate reads as "deepen the Episode collection module," not "refactor the RolloutHelper."

It writes in two places. The report goes to `<tmpdir>/architecture-review-<timestamp>.html`, outside the repo. During the grilling loop it adds or sharpens terms in `GLOSSARY.md` (creating it if needed), and offers to record a rejected candidate as an ADR so a future run doesn't suggest it again.

## Depth, and the report that hunts for it

The skill rests on one idea: **depth**. A deep module puts a lot of behaviour behind a small interface; a shallow one exposes its implementation through an interface nearly as wide as the code beneath it. The scan looks for:

- A concept you can't understand without bouncing between many small modules.
- Pure functions extracted only for testability, while the real bugs live in how they're called (no **locality**).
- Modules that leak across their **seams**.
- Code that is untested, or hard to test through its current interface.

Each candidate is a card with the files involved, the friction, a plain-English solution, the benefit in terms of **locality** and **leverage**, a before/after diagram, and a strength badge:

| Badge | What it means for you |
| --- | --- |
| `Strong` | The deletion test passes clearly and the friction is real. Take these seriously. |
| `Worth exploring` | Plausible, but the payoff depends on where the code is going next. |
| `Speculative` | Included for completeness. You can ignore most of these. |

The report ends with a **Top recommendation**, then the skill stops and asks which candidate you want to explore. At that point nothing is decided and no code has changed. Once you pick one, a [grilling](../productivity/grilling.md) session walks the constraints, what goes behind the seam, and which tests survive. The output is a decision, not a diff.

## Common questions

**It gave me a dozen candidates. Do I work through them in one session?**
One candidate per session. Several in one conversation fill the context with the report, the grilling, glossary edits, and code all at once. Pick one, grill it, take the decision into [to-spec](to-spec.md), and come back for the rest later.

**I just want the report, not an interview.**
Say so when you invoke it ("don't grill me, just show the report"). The design intent is that the report comes first and grilling starts only on a candidate you chose, but some models skip straight to interviewing you about their first idea.

**The report opened as unstyled HTML with no diagrams.**
It loads Tailwind and Mermaid from CDNs, so it needs network access when you open it. On an offline machine or a locked-down cluster node, ask for inline CSS and hand-built SVG diagrams instead.

**Will it ever tell me the codebase is fine?**
Rarely. It exists to produce findings, so it tends to find candidates. Use the badges to correct for that: if every card is `Speculative`, it found nothing.

## It's working if

- The candidates name your domain's concepts, not invented class names.
- The candidates sit in files you edited recently, not in corners nobody touches.
- No code changed during the run; the only new file is the HTML report in your temp directory.
- It stops after the report and asks which candidate you want.
- Each card explains the payoff as locality or leverage, and says which tests get simpler, not just "this is cleaner."

## Where it fits

`improve-codebase-architecture` is **periodic maintenance**, outside any chain: it queues up work rather than doing it. [codebase-design](codebase-design.md) owns the depth-and-seam vocabulary every candidate uses, and [domain-modeling](domain-modeling.md) keeps `GLOSSARY.md` and the ADRs current as the decision settles. The chosen candidate goes back into the main flow at [grill-with-docs](grill-with-docs.md) or [to-spec](to-spec.md). Its counterpart is [retro](retro.md): this skill improves the code the agent works in, `retro` improves the environment around it. For which skill fits a situation, [ask](ask.md) routes you over the whole set.
