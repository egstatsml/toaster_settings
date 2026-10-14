---
name: implement
description: "Implement a piece of work based on a spec or set of tickets in plans/."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

If the user passes a ticket path or number, read it from `plans/<slug>/tickets/` and state its title before starting. If the reference is ambiguous, ask. Read the spec the ticket points at too. Set the ticket's `Status:` to `in-progress`.

Call the Skill tool with "tdd" where possible, at pre-agreed seams.

Run the linter and type checker regularly (if the project has them), single test files regularly, and the full test suite once at the end. Before finishing, run the code path end to end on a tiny input (a few batches, a few steps, CPU if possible) to show it runs; never launch a full training run or sweep without the user asking.

Once done, call the Skill tool with "code-review" to review the work.

Tick the ticket's acceptance criteria, set its `Status:` to `done`, and add a short `## Notes` entry for anything the next ticket needs to know. Commit your work, including the updated ticket file, to the current branch.
