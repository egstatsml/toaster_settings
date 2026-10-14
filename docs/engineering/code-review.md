## What it does

`code-review` reviews the diff between `HEAD` and a fixed point you name (a commit, a branch, a tag, `main`, `HEAD~5`) along two axes. **Standards** asks whether the code follows how this repo writes code and avoids the mistakes that silently invalidate results. **Spec** asks whether the code does what the originating spec or ticket asked for. Each axis runs in its own sub-agent so neither sees the other's reasoning.

The skill never merges or re-ranks the two axes. The report ends with a worst finding *per axis* and declines to name a single winner across them. Code that follows every convention but implements the wrong thing passes Standards and fails Spec; code that does exactly what the ticket asked but leaks the test set into normalisation does the reverse. A blended verdict lets the passing axis hide the failing one.

## When to reach for it

Type `/code-review`, or the agent reaches for it automatically when you ask to review a branch, work in progress, or anything "since X".

| Your situation | Reach for |
| --- | --- |
| A diff exists and you want to know if it is built right *and* is the right thing | `code-review` |
| Nothing is written yet and you want it written test-first | [tdd](tdd.md) |
| A ticket needs building, review included | [implement](implement.md), which calls this skill itself |
| The whole codebase has drifted, not one diff | [improve-codebase-architecture](improve-codebase-architecture.md) |
| Something is broken and you do not know why | [diagnosing-bugs](diagnosing-bugs.md) |

You must supply the fixed point; if you do not, the skill asks rather than guessing. It checks that the ref resolves and the diff is non-empty before spawning anything, so a mistyped branch name fails in front of you.

## Prerequisites

The Standards axis needs nothing: it reads whatever the repo documents (`CODING_STANDARDS.md`, `CONTRIBUTING.md`, and the like) and always carries its built-in baselines.

The Spec axis needs a spec it can find. It looks for a path you pass, then ticket or spec paths named in the commits or the diff, then a `plans/<slug>/spec.md` matching the branch or feature, then asks you. With no spec at all, the report says "no spec available" rather than inventing requirements from the code.

## The two axes

| | Standards | Spec |
| --- | --- | --- |
| Question | Is it built right? | Is it the right thing? |
| Reads | The repo's documented standards, plus the two baselines | The originating spec or ticket under `plans/<slug>/` |
| Reports | Documented breaches (can be hard), baseline items (always judgement calls) | Missing or partial requirements, scope creep, requirements implemented wrongly |
| Every finding cites | The standards file and rule, or the named baseline item plus the hunk | The line of the spec |

The repo's own documentation is the primary source on the Standards axis, and **the repo always overrides** the baselines. Underneath it sit two baselines that apply even when a repo documents nothing, each item a labelled heuristic with its fix attached:

- **Research-correctness baseline**, raised first because a bug here produces a plausible number that is wrong: data leakage, evaluation on the wrong split, train/eval mode, silent broadcasting, wrong reduction, unseeded or unrecorded randomness, hard-coded experimental values, numerical instability, device and dtype drift.
- **Smell baseline**, a fixed set of Fowler code smells: Mysterious Name, Duplicated Code (the copy-pasted training loop), Data Clumps, Primitive Obsession, Repeated Switches, Shotgun Surgery, Speculative Generality, Dead Experiment Code.

Both axes skip anything your linter or type checker already enforces.

## Common questions

**Should I run it in the same session that wrote the code?**

Prefer a fresh one. A review in the authoring session has every assumption that shaped the code in its context, which makes it closer to confirmation than review. [implement](implement.md) runs it in-session as its closing step; for an independent pass, invoke `/code-review` yourself from a clean session.

**Can I trust the findings?**

Not without checking. Sub-agent output is a hypothesis: the skill aggregates the two reports without re-verifying each claim, so a finding can cite the wrong location or overstate an impact. Every finding carries a citation (a rule, a baseline item plus its hunk, or a spec line), and that is what makes it checkable. A "possible data leakage" is worth checking every time, even when it turns out to be deliberate.

**Why does it find new problems every time I run it?**

Each fix adds new code to review, and the judgement-call half of the Standards axis varies from run to run. There is no convergence guarantee. Act on the findings with a cited rule or a research-correctness item behind them, then stop.

**Does it review my uncommitted work?**

No. It diffs `<fixed-point>...HEAD`, which excludes staged and working-tree changes. Commit first, then review, then amend or add a fixup.

## It's working if

- It refuses to start on a bad ref or an empty diff, before any sub-agent is spawned.
- The report arrives as two separate blocks under `## Standards` and `## Spec`, not one merged list.
- Research-correctness findings (leakage, wrong split, eval mode, broadcasting) appear ahead of style smells.
- Every Standards finding names a rule in your repo or a baseline item with the hunk quoted; every Spec finding quotes a line of the spec.
- With no spec available, the Spec block says so instead of listing requirements it inferred from the code.

## Where it fits

`code-review` is the review step near the tail of the build chain: `grill-with-docs → to-spec → to-tickets → implement → code-review → retro`. It also stands alone on any branch you point it at.

- [implement](implement.md) drives the build and calls this skill as its closing review.
- [retro](retro.md) comes after and tunes it: when a session shows the review missing a class of mistake, `retro` proposes the check or the `CODING_STANDARDS.md` rule the Standards axis then reads.
- [to-spec](to-spec.md) and [to-tickets](to-tickets.md) produce the document the Spec axis checks against, so a vague spec makes that axis vague.

[ask](ask.md) routes across the whole set when you are unsure which skill the situation wants.
