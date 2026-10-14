# Research Engineering Skills

Agent skills for writing machine learning research code: plan the work, build it test-first, review it, and debug it. Plans and tickets are markdown files in the repo; there is no issue tracker and no pull request step.

## Install

```bash
git clone https://github.com/egstatsml/toaster_settings.git
cd toaster_settings
scripts/link-skills.sh
```

This symlinks every skill into `~/.claude/skills` (Claude Code) and `~/.agents/skills` (Codex and other Agent Skills harnesses). A `git pull` keeps them current; re-run the script after adding, removing, or renaming a skill.

## The flow

```txt
grill-with-docs → (prototype) → to-spec → to-tickets → implement (tdd + code-review) → retro
```

1. **Sharpen the idea.** `/grill-with-docs` interviews you about the research question, hypothesis, data, baselines, metrics, and compute, and records the project's vocabulary in `GLOSSARY.md` and hard-to-reverse decisions in `docs/adr/`.
2. **Answer runnable questions.** `/prototype` builds a throwaway script or notebook when a question can't be settled on paper.
3. **Write the plan.** `/to-spec` writes `plans/<slug>/spec.md`; `/to-tickets` splits it into `plans/<slug>/tickets/NN-<slug>.md`.
4. **Build it.** `/implement` works one ticket at a time, test-first via `tdd`, smoke-runs on a tiny input, and reviews via `code-review` before committing.
5. **Improve the environment.** `/retro` suggests checks, standards, and steering changes after a session.

When something breaks, `diagnosing-bugs` runs a disciplined loop that starts from a red feedback command. When you need the literature or a library's real behaviour, `research` sends a background agent to the primary sources. Not sure which to use? Type `/ask`.

### Plan layout

```txt
plans/
└── <slug>/
    ├── spec.md
    ├── research/<topic>.md
    └── tickets/
        ├── 01-<slug>.md
        └── 02-<slug>.md
```

Each spec and ticket carries a `Status:` line (`todo` → `in-progress` → `done` for tickets).

## Reference

**User-invoked** skills run only when you type them; they orchestrate. **Model-invoked** skills can also be picked up by the agent when the task fits; they hold the reusable discipline.

### Engineering

**User-invoked**

- **[ask](./skills/engineering/ask/SKILL.md)**: Ask which skill or flow fits your situation. A router over the skills in this repo.
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.md)**: Grilling session that also builds your project's domain model, sharpening terminology and updating `GLOSSARY.md` and ADRs inline.
- **[to-spec](./skills/engineering/to-spec/SKILL.md)**: Turn the current conversation into a research spec at `plans/<slug>/spec.md`. No interview, just synthesis.
- **[to-tickets](./skills/engineering/to-tickets/SKILL.md)**: Break a plan, spec, or conversation into tracer-bullet tickets under `plans/<slug>/tickets/`, each declaring its blocking edges.
- **[implement](./skills/engineering/implement/SKILL.md)**: Build the work described by a spec or ticket, driving `/tdd` at pre-agreed seams and closing out with `/code-review` before committing.
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.md)**: Scan a codebase for deepening opportunities, present them as a visual HTML report, then grill through whichever one you pick.
- **[retro](./skills/engineering/retro/SKILL.md)**: Suggest improvements to the coding agent's environment after a session, most severe first.

**Model-invoked**

- **[tdd](./skills/engineering/tdd/SKILL.md)**: Test-driven development for research code, one red-green slice at a time at agreed seams.
- **[code-review](./skills/engineering/code-review/SKILL.md)**: Two-axis review of the diff since a fixed point: **Standards** (repo standards, research-correctness baseline, smell baseline) and **Spec**, run as parallel sub-agents.
- **[diagnosing-bugs](./skills/engineering/diagnosing-bugs/SKILL.md)**: Diagnosis loop for hard bugs, silent training failures, and performance regressions.
- **[research](./skills/engineering/research/SKILL.md)**: Investigate a question against papers, reference implementations, and official docs, and capture cited findings in a Markdown file.
- **[prototype](./skills/engineering/prototype/SKILL.md)**: Build a throwaway script or notebook to answer one research or design question.
- **[domain-modeling](./skills/engineering/domain-modeling/SKILL.md)**: Build and sharpen the project's domain model, updating `GLOSSARY.md` and ADRs inline.
- **[codebase-design](./skills/engineering/codebase-design/SKILL.md)**: Shared vocabulary for designing deep modules: small interfaces, clean seams, testable through the interface.

### Productivity

**User-invoked**

- **[grill-me](./skills/productivity/grill-me/SKILL.md)**: Get relentlessly interviewed about a plan or design until every branch of the design tree is resolved.
- **[handoff](./skills/productivity/handoff/SKILL.md)**: Compact the current conversation into a handoff document so another agent can continue the work.
- **[wait-what](./skills/productivity/wait-what/SKILL.md)**: Re-pitch a message that didn't land, in plain English, using your `GLOSSARY.md` vocabulary.

**Model-invoked**

- **[grilling](./skills/productivity/grilling/SKILL.md)**: The reusable interview primitive behind `grill-me`, `grill-with-docs`, and `improve-codebase-architecture`.
- **[writing-for-agents](./skills/productivity/writing-for-agents/SKILL.md)**: Writing documents for agents: skills, AGENTS.md/CLAUDE.md, and any doc an agent reaches by a pointer.
