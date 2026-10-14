---
name: research
description: Investigate a question against high-trust primary sources (papers, reference implementations, official docs) and capture the findings as a cited Markdown file in the repo. Use when the user wants a method, paper, or library researched, related work or baselines gathered, implementation details checked against a paper, or reading legwork delegated to a background agent.
---

Spin up a **background agent** to do the research, so you keep working while it reads.

Its job:

1. Investigate the question against **primary sources**, not a secondary write-up of them. Follow every claim back to the source that owns it:
   - For a method or result: the paper itself (the arXiv or published version, including the appendix), and the authors' reference implementation where one exists. Where the paper and the code disagree on a detail (a normalisation, a learning-rate schedule, a loss term), report both.
   - For a library or framework: its official documentation and source code, for the version this repo pins.
   - For a dataset: its datasheet or paper, its official loader, and its licence.
2. Write the findings to a single Markdown file, citing each claim's source (paper section, equation, or table number; file and line for code). Record exact hyperparameters, dataset splits, and reported numbers where they matter, since those are what reproduction needs.
3. Save it where the repo already keeps such notes; with no convention, save it next to the plan it feeds (`plans/<slug>/research/<topic>.md`), or `docs/research/<topic>.md` when there is no plan, and say where.
