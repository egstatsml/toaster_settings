Agent skills for machine learning research code. Skills are organized into bucket folders under `skills/`:

- `engineering/`: writing research code (planning, implementation, testing, review, debugging)
- `productivity/`: non-code workflow tools

Plans and tickets are markdown files under `plans/<slug>/` in the repo being worked on (see the "Plan layout" section of `README.md`). Skills must not depend on an issue tracker, pull requests, or GitHub.

Every skill must have an entry in the top-level `README.md` and in its bucket's `README.md`, with the skill name linked to its `SKILL.md` and a one-line description. Both group entries into **User-invoked** and **Model-invoked**.

Every skill also has a human-facing docs page at `docs/<bucket>/<skill-name>.md`. When you add, rename, or change the behaviour of a skill, create or re-sync its docs page following [.agents/writing-docs.md](./.agents/writing-docs.md). When you remove a skill, delete its page. `docs/` may also hold documentation generated for source code; leave anything outside `docs/engineering/` and `docs/productivity/` alone.

Every `SKILL.md` is either user-invoked (`disable-model-invocation: true` plus `policy.allow_implicit_invocation: false` in `agents/openai.yaml`, reachable only by the human) or model-invoked (model- or user-reachable). See [.agents/invocation.md](./.agents/invocation.md).

[`ask`](./skills/engineering/ask/SKILL.md) is the router that maps every user-reachable skill and how they relate. Whenever you add, rename, remove, or change how a skill fits the flows, re-read `ask`'s `SKILL.md` and update it so the map stays accurate.

To (re)link every skill into the local harness skill directories (`~/.claude/skills`, `~/.agents/skills`), run `scripts/link-skills.sh`. Re-run it after adding, removing, or renaming a skill.

No em-dashes anywhere in this repo's prose (`SKILL.md` files, docs, `README.md`, code comments). Where a sentence reaches for one, rewrite it instead with a comma, colon, period, parentheses, or a conjunction, whichever the sentence actually wants; never do a blind character substitution.
