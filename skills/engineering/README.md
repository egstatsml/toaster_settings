# Engineering

Skills for writing research code: planning, implementation, testing, review, and debugging.

## User-invoked

Reachable only when you type them (Claude Code: `disable-model-invocation: true`; Codex: `policy.allow_implicit_invocation: false` in `agents/openai.yaml`).

- **[ask](./ask/SKILL.md)**: Ask which skill or flow fits your situation. A router over the skills in this repo.
- **[grill-with-docs](./grill-with-docs/SKILL.md)**: Grilling session that also builds your project's domain model, sharpening terminology and updating `GLOSSARY.md` and ADRs inline.
- **[to-spec](./to-spec/SKILL.md)**: Turn the current conversation into a research spec at `plans/<slug>/spec.md`: research question, hypothesis, experiments, implementation and testing decisions.
- **[to-tickets](./to-tickets/SKILL.md)**: Break a plan, spec, or conversation into tracer-bullet tickets, one markdown file each under `plans/<slug>/tickets/`, each declaring its blocking edges.
- **[implement](./implement/SKILL.md)**: Build the work described by a spec or ticket, driving `/tdd` at pre-agreed seams, smoke-running on a tiny input, and closing out with `/code-review` before committing.
- **[improve-codebase-architecture](./improve-codebase-architecture/SKILL.md)**: Scan a codebase for deepening opportunities, present them as a visual HTML report, then grill through whichever one you pick.
- **[retro](./retro/SKILL.md)**: Suggest improvements to the coding agent's environment (navigation, automated checks, coding standards, steering files, tooling) after a session, most severe first.

## Model-invoked

Model- or user-reachable (rich trigger phrasing so the model can reach for them).

- **[tdd](./tdd/SKILL.md)**: Test-driven development for research code: red-green slices at agreed seams, with guidance on data contracts, known-answer losses and metrics, overfit-one-batch, and determinism tests.
- **[code-review](./code-review/SKILL.md)**: Two-axis review of the diff since a fixed point: **Standards** (the repo's coding standards, plus a research-correctness baseline and a Fowler smell baseline) and **Spec** (does it faithfully implement the spec or ticket?), run as parallel sub-agents.
- **[diagnosing-bugs](./diagnosing-bugs/SKILL.md)**: Disciplined diagnosis loop for hard bugs, silent training failures, and performance regressions: build a feedback loop that goes red on this bug → minimise → hypothesise → instrument → fix → regression-test.
- **[research](./research/SKILL.md)**: Investigate a question against primary sources (papers, reference implementations, official docs) and capture the findings as a cited Markdown file, run as a background agent.
- **[prototype](./prototype/SKILL.md)**: Build a throwaway script or notebook to answer one question: does this idea learn at all, what does this data look like, does this logic behave.
- **[domain-modeling](./domain-modeling/SKILL.md)**: Actively build and sharpen a project's domain model by challenging terms, stress-testing with scenarios, and updating `GLOSSARY.md` and ADRs inline.
- **[codebase-design](./codebase-design/SKILL.md)**: Shared discipline and vocabulary for designing deep modules: small interfaces, clean seams, testable through the interface.
