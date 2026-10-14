---
name: code-review
description: "Review the changes since a fixed point (commit, branch, tag, or merge-base) along two axes: Standards (does the code follow this repo's documented coding standards and avoid common ML research pitfalls?) and Spec (does the code match what the originating spec or ticket asked for?). Runs both reviews in parallel sub-agents and reports them side by side. Use when the user wants to review a branch, work-in-progress changes, or asks to \"review since X\"."
---

Two-axis review of the diff between `HEAD` and a fixed point the user supplies:

- **Standards**: does the code conform to this repo's documented coding standards?
- **Spec**: does the code faithfully implement the originating spec or ticket?

Both axes run as **parallel sub-agents** so they don't pollute each other's context, then this skill aggregates their findings.

## Process

### 1. Pin the fixed point

Whatever the user said is the fixed point (a commit SHA, branch name, tag, `main`, `HEAD~5`, etc.). If they didn't specify one, ask for it.

Capture the diff command once: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base). Also note the list of commits via `git log <fixed-point>..HEAD --oneline`.

Before going further, confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. A bad ref or empty diff should fail here, not inside two parallel sub-agents.

### 2. Identify the spec source

Look for the originating spec, in this order:

1. A path the user passed as an argument.
2. Ticket or spec paths mentioned in the commit messages or the diff itself (ticket files under `plans/<slug>/tickets/` change status as work lands).
3. A spec under `plans/<slug>/spec.md` matching the branch name or feature. When the work was one ticket, pass the ticket and its spec.
4. If nothing is found, ask the user where the spec is. If they say there isn't one, the **Spec** sub-agent will skip and report "no spec available".

### 3. Identify the standards sources

Search the repo for every file that documents how code should be written. When `CODING_STANDARDS.md` or `CONTRIBUTING.md` exists, it must be on the list.

On top of whatever the repo documents, the Standards axis always carries two baselines that apply even when a repo documents nothing: a **research-correctness baseline** (the mistakes that silently invalidate results) and a **smell baseline** (a fixed set of Fowler code smells, _Refactoring_, ch.3). Two rules bind both:

- **The repo overrides.** A documented repo standard always wins; where it endorses something a baseline would flag, suppress the finding.
- **Always a judgement call.** Each baseline item is a labelled heuristic ("possible data leakage"), never a hard violation. Like any standard here, skip anything tooling already enforces.

Each item reads *what it is* → *how to fix*; match it against the diff.

**Research-correctness baseline.** These are the findings most worth raising: a bug here produces a plausible number that is wrong.

- **Data leakage**: test or validation data reaches training, normalisation statistics or vocabularies are fitted on more than the training split, or a split is made after shuffling grouped data. → fit everything on the training split only; split before any fitting.
- **Evaluation on the wrong split**: model selection, early stopping, or tuning reads the test set. → select on validation, report test once.
- **Train/eval mode**: a model evaluated without switching to eval mode (dropout, batch norm still training), or gradients tracked during evaluation. → switch modes explicitly around evaluation and disable gradients.
- **Silent broadcasting or shape mismatch**: an operation between tensors of different shapes that broadcasts instead of failing (a `(N,)` against an `(N, 1)` loss target is the classic). → assert or document shapes at module boundaries.
- **Wrong reduction**: a loss or metric averaged over the wrong axis, over padding, or averaged per batch when it should be per example. → mask padding and reduce over the intended axis.
- **Unseeded or uncaptured randomness**: a run whose seed, config, or data version is not recorded with its results. → seed every source of randomness and save the resolved config next to the outputs.
- **Hard-coded experimental values**: a hyperparameter, path, or dataset choice buried in code where the config cannot see it. → lift it into the config.
- **Numerical instability**: `log` of a probability, `exp` of a logit, or division without an epsilon, where a stable fused form exists. → use the library's stable form (log-softmax, logsumexp, the loss that takes logits).
- **Device and dtype drift**: tensors created on the wrong device or in the wrong dtype inside the model, or a `.item()`/`.cpu()` sync inside the hot loop. → create tensors from existing ones' device and dtype; keep syncs out of the loop.

**Smell baseline.**

- **Mysterious Name**: a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code**: the same logic shape appears in more than one hunk or file in the change (a copy-pasted training loop is the usual one). → extract the shared shape, call it from both.
- **Data Clumps**: the same few fields or params keep travelling together. → bundle them into one config object or dataclass, pass that.
- **Primitive Obsession**: a string or bare tuple standing in for a concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches**: the same `if model_type == ...` cascade recurs across the change. → one registry or mapping both sites share.
- **Shotgun Surgery**: one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Speculative Generality**: abstraction, parameters, or hooks added for needs the spec doesn't have. → delete it; inline back until a real need shows.
- **Dead Experiment Code**: commented-out variants, unused flags, or abandoned branches of an experiment left in place. → delete it; git keeps the history.

### 4. Spawn both sub-agents in parallel

Issue both sub-agent calls together, in the foreground, and aggregate the reports they return.

**Standards sub-agent prompt** should include:

- The full diff command and commit list.
- The list of standards-source files you found in step 3, **plus both baselines from step 3** pasted in full (the sub-agent has no other access to it).
- The brief: "Report, per file/hunk where relevant, (a) every place the diff violates a documented standard: cite the standard (file + the rule); and (b) any baseline item you spot: name it and quote the hunk, research-correctness items first. Distinguish hard violations from judgement calls: documented-standard breaches can be hard, but baseline items are always judgement calls, and a documented repo standard overrides the baselines. Skip anything tooling enforces. Under 400 words."

**Spec sub-agent prompt** should include:

- The diff command and commit list.
- The path or fetched contents of the spec.
- The brief: "Report: (a) requirements the spec asked for that are missing or partial; (b) behaviour in the diff that wasn't asked for (scope creep); (c) requirements that look implemented but where the implementation looks wrong. Quote the spec line for each finding. Under 400 words."

If the spec is missing, skip the Spec sub-agent and note this in the final report.

### 5. Aggregate

Present the two reports under `## Standards` and `## Spec` headings, verbatim or lightly cleaned. Do **not** merge or rerank findings, because the two axes are deliberately separate (see _Why two axes_).

End with a one-line summary: total findings per axis, and the worst issue _within each axis_ (if any). Don't pick a single winner across axes: that's the reranking the separation exists to prevent.

## Why two axes

A change can pass one axis and fail the other:

- Code that follows every standard but implements the wrong thing → **Standards pass, Spec fail.**
- Code that does exactly what the spec asked but breaks the project's conventions → **Spec pass, Standards fail.**

Reporting them separately stops one axis from masking the other.
