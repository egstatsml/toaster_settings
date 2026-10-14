# GLOSSARY.md Format

## Structure

```md
# {Context Name}

{One or two sentence description of what this context is and why it exists.}

## Language

**Episode**:
{A one or two sentence description of the term}
_Avoid_: Trajectory, rollout

**Probe**:
A small classifier trained on frozen representations to measure what they encode.
_Avoid_: Head, readout

**Held-out split**:
Examples never seen during training or model selection, used once for the final number.
_Avoid_: Test set (when it is ambiguous with the validation split), eval set
```

## Rules

- **Be opinionated.** When multiple words exist for the same concept, pick the best one and list the others under `_Avoid_`.
- **Keep definitions tight.** One or two sentences max. Define what it IS, not what it does.
- **Only include terms specific to this project's context.** General programming and ML concepts (timeouts, error types, learning rate, batch size) don't belong even if the project uses them extensively. Before adding a term, ask: is this a concept unique to this context, or a general programming concept? Only the former belongs.
- **Group terms under subheadings** when natural clusters emerge. If all terms belong to a single cohesive area, a flat list is fine.

## Single vs multi-context repos

**Single context (most repos):** One `GLOSSARY.md` at the repo root.

**Multiple contexts:** A `GLOSSARY-MAP.md` at the repo root lists the contexts, where they live, and how they relate to each other:

```md
# Glossary Map

## Contexts

- [Data](./src/data/GLOSSARY.md): builds datasets, splits, and batches from raw sources
- [Modelling](./src/models/GLOSSARY.md): model architectures and objectives
- [Evaluation](./src/evaluation/GLOSSARY.md): metrics, benchmarks, and result reporting

## Relationships

- **Data → Modelling**: Data yields batches in the shape contract Modelling consumes
- **Modelling → Evaluation**: Evaluation loads checkpoints produced by Modelling and scores them on held-out splits
- **Data ↔ Evaluation**: Shared split definitions, so evaluation never sees training examples
```

The skill infers which structure applies:

- If `GLOSSARY-MAP.md` exists, read it to find contexts
- If only a root `GLOSSARY.md` exists, single context
- If neither exists, create a root `GLOSSARY.md` lazily when the first term is resolved

When multiple contexts exist, infer which one the current topic relates to. If unclear, ask.
