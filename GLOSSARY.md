# Research Engineering Skills

A collection of agent skills (slash commands and behaviors) for writing machine learning research code. Skills are organized into buckets and work against plan files kept in the target repo.

## Language

**Plan**:
A directory `plans/<slug>/` in the target repo holding one piece of work: its **Spec**, its **Tickets**, and any research notes.
_Avoid_: issue tracker, backlog

**Spec**:
The `spec.md` inside a **Plan**: research question, hypothesis, experiments, and the implementation and testing decisions for the code.
_Avoid_: PRD, design doc

**Ticket**:
One markdown file under a **Plan**'s `tickets/` directory describing a tracer-bullet slice of work, with a `Status:` line and its blocking edges.
_Avoid_: issue, task card

**Seam**:
The public interface a test observes behavior at, agreed with the user before any test is written.

## Relationships

- A **Plan** holds one **Spec** and many **Tickets**
- A **Ticket** may be blocked by other **Tickets** in the same **Plan**

## Flagged ambiguities

- "issue" was previously used for both a ticket and a remote tracker entry. Resolved: there is no remote tracker; the unit of work is a **Ticket** file.
