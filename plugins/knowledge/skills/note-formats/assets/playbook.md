---
tags:
  - playbook
  - topic
---

# Playbook - [Category of Situation]

*If you can write this as one ordered list of steps with no branching, it is a Runbook, not a Playbook. Most tasks are Runbooks — do not reach for this shape by default.*

**Scope** — *What category of situation this covers. Be specific enough that you know when you have left scope.*

**When to use this** — *The trigger that sends you here rather than straight to a known runbook.*

---

## Rationale

*Authoring-time notes on why the Decision Guide below is shaped as it is — why one path wins over another, why an edge case falls under "investigate further" rather than getting its own path. Not read while navigating a live situation.*

- **[Decision area]:** [the choice made and why]

---

## Context Variables

*Only needed where the same details apply across multiple paths — a person, an account, a date. Delete entirely if each path is self-contained.*

| Variable | Value |
|---|---|
| [Variable name] | |

---

## Decision Guide

*How you work out which path applies. A table mapping situation to path, or a short decision tree. This is the core of the playbook — everything else supports it.*

| Situation | Path |
|---|---|
| [Condition A] | [[Runbook A]] |
| [Condition B] | [[Runbook B]] |
| Unclear, or none of the above | Investigate further below |

---

## Roles and Responsibilities

*Optional. Who needs to be involved and what their job is. Delete for solo tasks.*

| Role | Responsibility | Who |
|---|---|---|
| | | |

---

## Paths

### [Path A Name]

*When this path applies, and anything to know before handing off.*

Execute: [[Runbook A]]

### [Path B Name]

*Same.*

Execute: [[Runbook B]]

---

## Communication

*Optional. Who needs to be told what, and when. Delete if there is no one else to update.*

---

## Execution Log

*Optional. Skip for genuine one-offs. Keep for recurring situations — it records which path was taken and whether the Decision Guide still held up.*

| Date | Path taken | Notes |
|---|---|---|
| | | First execution |

---

## Related Topics

*Delete if nothing qualifies.*

- [[Related Standard]]
