---
tags:
  - runbook
  - topic
---

# Runbook - [Descriptive Task Name]

*Title the runbook after the task it performs. A runbook is for known tasks — if you are discovering the steps as you go, write it up afterwards, once the steps are confirmed to work.*

**Trigger** — *The event that initiates this runbook, e.g. "a new client signs a contract".*

**Objective** — *What success looks like. One sentence.*

**Estimated time** — *Rough duration, so you know whether to start now or block out time.*

---

## Rationale

*Authoring-time notes recording the decisions baked into the steps below. Not read during execution — its job is to let you update the runbook intelligently later. If the source material contains no reasoning, leave this placeholder rather than inventing justifications.*

- **[Decision area]:** [the choice made and why]

---

## Before You Start

*Prerequisites, access, or tools needed in hand before beginning. Delete this section if there is nothing to prepare.*

- [ ] Prerequisite
- [ ] Prerequisite

---

## Context Variables

*Only needed where the runbook is executed repeatedly with different specifics — a client name, an environment, a domain. Fill these in before executing and reference these exact values in the steps. Delete the section entirely for one-off or always-identical tasks.*

| Variable | Value |
|---|---|
| [Variable name] | |

---

## Steps

*Plain numbered steps for short runbooks. Group into `### Phase N: [Name]` subheadings only once the step count is long enough that grouping aids scanning — do not add phases by default.*

1. **[Step name]** — what to do.

   ```bash
   command --if-applicable
   ```

   *Expected result: what you should see if this worked.*

2. **[Step name]** — what to do.

   *Expected result: what you should see if this worked.*

> [!warning]
> Call out anything destructive, irreversible, or easy to get wrong. Delete this callout if nothing qualifies.

---

## Verification

*The final check that the objective was achieved — confirmation of the end state, not a repeat of each step's expected result. A check, a URL to visit, a value to compare.*

- [ ] Confirm outcome
- [ ] Confirm outcome

---

## Rollback

*Optional. How to undo this, or who to contact if a step fails. Delete if the task has no failure mode worth documenting.*

---

## Execution Log

*Optional. Skip for genuine one-offs. Keep for anything you expect to run again — it records when this was last done and whether the steps still held up.*

| Date | Notes |
|---|---|
| | First execution |

---

## Related Topics

*The standard this runbook follows, the playbook that routes to it, or adjacent runbooks. Delete if nothing qualifies.*

- [[Related Standard or Playbook]]
