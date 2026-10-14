---
name: to-spec
description: "Turn the current conversation into a research spec saved as plans/<slug>/spec.md: no interview, just synthesis of what you've already discussed."
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a spec for a piece of research code: a new model, an experiment, a data pipeline, an evaluation. Do NOT interview the user; just synthesize what you already know.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the spec, and respect any ADRs in the area you're touching.

2. Sketch out the seams at which you're going to test the code. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one. In research code the usual seams are the data pipeline's output (batches, shapes, dtypes, splits), a model's forward pass, a loss or metric function, and a short end-to-end training run on a tiny fixture.

Check with the user that these seams match their expectations.

3. Pick a short kebab-case slug for the work and write the spec to `plans/<slug>/spec.md` using the template below, creating the directory if needed. Tell the user the path.

<spec-template>

# <Title>

**Status:** planned

## Research Question

The question this work exists to answer, or the problem it removes, in one or two paragraphs. If the code is infrastructure rather than an experiment, say which research it unblocks.

## Hypothesis

What we expect to find, stated so it can be wrong: the result that would support it and the result that would refute it. Omit this section for pure infrastructure.

## Approach

The method, from the researcher's perspective: model, objective, data, and how the pieces fit together.

## Experiments

One entry per experiment or run family:

- **What it tests**: which part of the hypothesis
- **Data**: datasets, splits, preprocessing, and how train/validation/test stay separated
- **Baselines and ablations**: what the result is compared against
- **Metrics**: what is measured, on which split, and how many seeds
- **Expected outcome**: what a supporting and a refuting result look like

## Requirements

A numbered list of everything the code must do for the experiments above to run and be trusted. Be extensive: data loading, model variants, configuration, logging, checkpointing, evaluation, result reporting.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Configuration shape (what is a config option vs hard-coded)
- Data formats and tensor shapes at module boundaries
- Technical clarifications from the researcher
- Architectural decisions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (a loss function, a shape contract, a config schema), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts.

## Reproducibility

How a result produced by this code can be reproduced: seeding, config capture, environment and dependency pinning, data versioning, where runs, logs, and checkpoints are written.

## Compute Budget

Rough hardware and time per run, and the total across the experiments. Note anything that must run on a cluster rather than locally.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- The agreed seams, and what each one catches
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this spec.

## Further Notes

Any further notes, including related papers and prior results.

</spec-template>
