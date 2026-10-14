# Good and Bad Tests

## Good Tests

**Integration-style**: Test through real interfaces, not mocks of internal parts.

```python
# GOOD: Tests observable behavior
def test_loader_never_yields_test_split_examples():
    loader = make_loader(tiny_dataset, split="train", batch_size=4)
    seen = {i for batch in loader for i in batch["example_id"].tolist()}
    assert seen.isdisjoint(tiny_dataset.split_ids("test"))
```

Characteristics:

- Tests behavior callers care about
- Uses public API only
- Survives internal refactors
- Describes WHAT, not HOW
- One logical assertion per test

```python
# GOOD: Known answer, worked by hand from the definition
def test_masked_mean_ignores_padding():
    values = torch.tensor([[1.0, 3.0, 99.0]])
    mask = torch.tensor([[True, True, False]])
    assert masked_mean(values, mask).item() == pytest.approx(2.0)

# GOOD: End-to-end wiring check on a tiny batch
def test_model_can_overfit_one_batch():
    torch.manual_seed(0)
    model, batch = make_model(tiny_config), tiny_batch()
    losses = train_steps(model, batch, steps=50)
    assert losses[-1] < 0.05 * losses[0]
```

## Bad Tests

**Implementation-detail tests**: Coupled to internal structure.

```python
# BAD: Tests implementation details
def test_forward_calls_attention(mocker):
    spy = mocker.spy(model.encoder.layers[0], "attention")
    model(batch)
    assert spy.call_count == 1
```

Red flags:

- Mocking internal collaborators (your own layers, your own data transforms)
- Testing private helpers
- Asserting on call counts/order
- Test breaks when refactoring without behavior change
- Test name describes HOW not WHAT
- Asserting exact float equality instead of a tolerance

**Results-as-tests**: asserting a research outcome on real training.

```python
# BAD: Slow, flaky, and tests the method rather than the code
def test_model_reaches_good_accuracy():
    model = train(full_config)
    assert evaluate(model, "test")["accuracy"] > 0.9
```

That belongs in an experiment and its write-up, not the test suite.

**Tautological tests**: Expected value restates the implementation, so the test passes by construction.

```python
# BAD: Expected value is recomputed the way the code computes it
def test_mse():
    x, y = torch.randn(8), torch.randn(8)
    assert mse(x, y) == ((x - y) ** 2).mean()

# GOOD: Expected value is an independent, known literal
def test_mse():
    assert mse(torch.tensor([1.0, 2.0]), torch.tensor([1.0, 4.0])).item() == pytest.approx(2.0)
```
