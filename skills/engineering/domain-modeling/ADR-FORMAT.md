# ADR Format

ADRs live in `docs/adr/` and use sequential numbering: `0001-slug.md`, `0002-slug.md`, etc.

Create the `docs/adr/` directory lazily: only when the first ADR is needed.

## Template

```md
# {Short title of the decision}

{1-3 sentences: what's the context, what did we decide, and why.}
```

That's it. An ADR can be a single paragraph. The value is in recording *that* a decision was made and *why*, not in filling out sections.

## Optional sections

Only include these when they add genuine value. Most ADRs won't need them.

- **Status** frontmatter (`proposed | accepted | deprecated | superseded by ADR-NNNN`): useful when decisions are revisited
- **Considered Options**: only when the rejected alternatives are worth remembering
- **Consequences**: only when non-obvious downstream effects need to be called out

## Numbering

Scan `docs/adr/` for the highest existing number and increment by one.

## When to offer an ADR

All three of these must be true:

1. **Hard to reverse**: the cost of changing your mind later is meaningful
2. **Surprising without context**: a future reader will look at the code and wonder "why on earth did they do it this way?"
3. **The result of a real trade-off**: there were genuine alternatives and you picked one for specific reasons

If a decision is easy to reverse, skip it: you'll just reverse it. If it's not surprising, nobody will wonder why. If there was no real alternative, there's nothing to record beyond "we did the obvious thing."

### What qualifies

- **Architectural shape.** "Every model is built from a config object; nothing reads global state." "Preprocessing runs once offline and is cached, not done in the data loader."
- **Evaluation protocol.** "Splits are grouped by subject, so no subject appears in both train and test." "We report the mean and standard deviation over five seeds." These decide whether results are comparable over time.
- **Technology choices that carry lock-in.** Framework (PyTorch vs JAX), experiment tracker, data format, cluster scheduler. Not every library: just the ones that would take weeks to swap out.
- **Boundary and scope decisions.** "This repo reproduces the paper's setting; extensions live in a separate package." The explicit no-s are as valuable as the yes-s.
- **Deliberate deviations from the obvious path.** "We implement the loss by hand instead of using the library version because it masks padding differently." Anything where a reasonable reader would assume the opposite. These stop the next person from "fixing" something that was deliberate.
- **Constraints not visible in the code.** "The dataset licence forbids redistributing processed copies." "Every run must fit on one 24 GB GPU."
- **Rejected alternatives when the rejection is non-obvious.** If you considered a learned positional encoding and picked a fixed one for subtle reasons, record it; otherwise someone will suggest it again in six months.
