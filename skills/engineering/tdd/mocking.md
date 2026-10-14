# When to Mock

Mock at **system boundaries** only:

- Experiment trackers and remote logging (W&B, MLflow, TensorBoard writers)
- Remote storage, dataset downloads, and model hubs
- Cluster schedulers and anything that launches jobs
- Time and randomness (prefer seeding over mocking)
- File system (sometimes: prefer a temporary directory)

Don't mock:

- Your own models, layers, losses, or data transforms
- Tensor libraries (PyTorch, JAX, NumPy): use tiny real tensors instead
- Anything you control

Prefer a **tiny real thing** over a mock: a ten-example in-memory dataset, a two-layer model with a hidden size of 8, a config with one epoch of two steps. These run in milliseconds and exercise the real code.

## Designing for Mockability

At system boundaries, design interfaces that are easy to replace:

**1. Use dependency injection**

Pass external dependencies in rather than creating them internally:

```python
# Easy to replace in a test
def train(model, loader, logger):
    for step, batch in enumerate(loader):
        logger.log({"loss": step_fn(model, batch)}, step=step)

# Hard to replace
def train(model, loader):
    logger = wandb.init(project="my-project")
    ...
```

**2. Prefer narrow interfaces over generic ones**

Give the boundary the few operations the code actually uses, so a test fake is a few lines:

```python
# GOOD: A small logger interface a test can satisfy with a list
class MetricLogger(Protocol):
    def log(self, metrics: dict[str, float], step: int) -> None: ...

# BAD: The whole tracker client passed everywhere, so a fake must imitate all of it
def train(model, loader, wandb_run): ...
```

The narrow approach means:

- Each fake records exactly what the test checks
- No conditional logic in test setup
- Easy to see which boundary a test exercises
