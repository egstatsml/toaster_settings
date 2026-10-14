## What it does

`codebase-design` fixes the words you use to design a module: **module**, **interface**, **depth**, **seam**, **adapter**, **leverage**, **locality**. It defines each one precisely, bans the loose substitutes ("component", "service", "API", "boundary"), and states the handful of principles that follow from them.

It is a reference, not a process. It runs no loop, produces no artifact, and never stops to ask you a question. The skills that touch design ([tdd](tdd.md), [improve-codebase-architecture](improve-codebase-architecture.md)) speak its vocabulary; on its own, it gives you the words and stops.

## When to reach for it

Type `/codebase-design`, or the agent reaches for it automatically when a design task fits.

Reach for it when you already know which code you're redesigning and need to think about its shape: where the seam goes, how small the interface can get, whether pulling a training loop or a data pipeline into one module removes complexity from its callers. Also use it to settle an argument about what a design word means.

| The problem | The skill |
| --- | --- |
| The shape of one module: its interface, its seam, its depth | `codebase-design` |
| The words of the domain: "step" means an optimiser step in one file and an environment step in another | [domain-modeling](domain-modeling.md) |
| You don't yet know which module to redesign | [improve-codebase-architecture](improve-codebase-architecture.md) |
| You want the design argued with, not just named | [grilling](../productivity/grilling.md) |
| A concrete behaviour to build, with tests that survive a refactor | [tdd](tdd.md) |

## The vocabulary

The glossary is the skill.

| Term | What it means | Don't say |
| --- | --- | --- |
| **Module** | Anything with an interface and an implementation. Scale-agnostic: a function, a class, a package. | unit, component, service |
| **Interface** | Everything a caller must know to use it correctly: the type signature, plus invariants, ordering constraints, error modes, required config, performance characteristics (tensor shapes and devices included). | API, signature |
| **Depth** | Leverage at the interface: how much behaviour a caller or test can exercise per unit of interface learned. **Deep**: a lot behind a small interface. **Shallow**: the interface is nearly as complex as the implementation. | none |
| **Seam** | A place you can alter behaviour without editing in that place: the *location* of an interface. Where to put it is its own decision. | boundary |
| **Adapter** | A concrete thing satisfying an interface at a seam. It names a role: a sharded on-disk dataset and a tiny in-memory one are both adapters. | none |
| **Leverage** | What callers get from depth: more capability per unit of interface. | none |
| **Locality** | What maintainers get from depth: change, bugs and verification stay in one place, so one fix covers every caller. | none |

Depth here is leverage, not the ratio of implementation lines to interface lines, because that ratio rewards padding the implementation.

## The four principles

- **Depth is a property of the interface, not the implementation.** A deep module can be built from small swappable parts; callers just don't see them.
- **The deletion test.** Imagine deleting the module. If complexity vanishes, it was a pass-through. If it reappears across N callers, it was earning its keep.
- **The interface is the test surface.** Callers and tests cross the same seam. If you want to test *past* the interface, the module is the wrong shape.
- **One adapter means a hypothetical seam. Two adapters means a real one.** Don't cut a seam (say, around the experiment logger) until something varies across it, typically a real adapter and a test one.

Two supporting files go further and are read on demand. [DEEPENING.md](../../skills/engineering/codebase-design/DEEPENING.md) classifies a module's dependencies (in-process, local-substitutable, remote but owned, true external) because the category decides how you test across the seam. [DESIGN-IT-TWICE.md](../../skills/engineering/codebase-design/DESIGN-IT-TWICE.md) has parallel sub-agents produce three or more radically different interfaces for one module, then compares them on depth, locality and seam placement.

## Common questions

**I pointed a session at it and it spent ages redesigning things I never asked about.**
The skill is a reference with no stopping rule, so an agent told to "drive" it invents a process, usually by grabbing the parallel sub-agents in `DESIGN-IT-TWICE.md`. Invoke a driver skill instead ([grill-with-docs](grill-with-docs.md), [improve-codebase-architecture](improve-codebase-architecture.md) or [tdd](tdd.md)) and let it use this vocabulary.

**Isn't a deep module just a folder layout?**
No. Depth is about the interface and accessing the module only through it, whatever the file system looks like. A tidy package tree can still be full of shallow modules. That's why **module** is defined as scale-agnostic.

**How do I stop code reaching past the interface?**
The skill defines what a deep module is; it doesn't enforce it. Python's options are conventions (a package that exports only its interface from `__init__.py`, leading underscores for internals) or an import-linting tool in your checks. If enforcement matters, that's an environment change for [retro](retro.md).

## It's working if

- Design conversations stop producing "component", "service" and "boundary", and start producing "module", "interface" and "seam".
- Someone can point at a proposed extraction and say whether it passes the deletion test, without hedging.
- A proposed seam comes with a second adapter named, not just the first.
- Discussion of an interface covers invariants, shapes and error modes, not only the type signature.
- Invoking it alone does not start a refactoring session.

## Where it fits

`codebase-design` is the **vocabulary layer** underneath the engineering skills rather than a step in any chain. Its closest neighbour is [domain-modeling](domain-modeling.md), the parallel reference for the domain's words rather than the module's shape; naming a deep module well needs both. [improve-codebase-architecture](improve-codebase-architecture.md) surveys for deepening candidates in this glossary, so it finds the module and you design it in these words. When you're unsure which skill fits, [ask](ask.md) routes you.
