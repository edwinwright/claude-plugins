---
name: acceptance-review
description: 'Close out a delivered work order: verify the built result against its acceptance criteria, promote business rules discovered during the build into the glossary and domain model, promote contested costly-to-reverse choices into decision records, and archive the spent requirements document and technical specification. Use when a work order is built, before it is considered done. Triggers: "does this meet the acceptance criteria", "close out this work order", "sign off this work", "verify against the requirements", "what did we learn building this".'
---

# acceptance-review

You are closing out a delivered work order. Three jobs, in this order:

1. **Verify** the built result against the acceptance criteria that were agreed.
2. **Harvest** what turned out to be durable — business rules into the glossary and domain model, contested choices into decision records.
3. **Archive** the spent documents so they stop being read as current.

The harvest is the point. Without it, everything learned during the build stays locked in a requirements document that describes a plan rather than a system, and the durable documentation goes stale one feature at a time. This skill is the mechanism that stops that.

## Routes

Runs at the close of the **Standard** and **Full** routes. Direct skips it: there is no requirements document to verify against and nothing durable to harvest — the tests and the code review are the acceptance. Routes are defined in `../request-triage/references/routes.md`.

**Scope: one run per work order,** after the last story is done. Not per story — a per-story harvest fragments the durable additions and makes the archive step incoherent.

---

## Step 1: Read the work order

Read `docs/work/YYYY-MM-<slug>/work-order.md`, then everything else in the folder: `requirements.md`, `tech-spec.md` and `delivery-plan.md` where the route produced them, and every file in `tickets/`.

Check the layout defaults in `../request-triage/references/artefacts.md` first — the host project may put work elsewhere, and older work may sit under `docs/features/<slug>/`.

**If `status` is already `accepted`,** stop and say so. Re-running would re-harvest content that is already in the durable documents and produce duplicates.

**If there is no work order,** say what is missing and offer to verify against the ticket files alone. Say clearly that you are doing so — verification against tickets is weaker than verification against agreed requirements, because tickets can be edited after the fact.

---

## Step 2: Run the two lenses in parallel

Read `prompts/verification-lens.md` and `prompts/harvest-lens.md`. Spawn both as anonymous subagents **in the same turn** via the Agent tool, using each file's contents as the system prompt. Do not use any globally registered agent — create fresh subagents with these exact instructions.

Configure each with:
- model: claude-sonnet-4-6
- effort: high for the Verification Lens, medium for the Harvest Lens

The Verification Lens needs to read the repository and run the verification commands. The Harvest Lens needs the work order documents and the diff of what was actually built.

Pass both:
- The full work order folder
- The diff or commit range for the work, if the user can identify it — `git log` since the work order was opened is a reasonable default

Wait for both to complete.

---

## Step 3: Present the verification result

Read the template at `acceptance-report-template.md` and present the result.

Every criterion comes back as `PASS`, `FAIL`, or `UNVERIFIABLE`, each with evidence.

> ### Gate
>
> **Stop here on any `FAIL` or `UNVERIFIABLE`.**

Report what did not pass and what would close it. Do not harvest, do not archive, do not amend anything. An unverified criterion is not a small problem to work around — it is the whole reason this step exists.

`UNVERIFIABLE` blocks as firmly as `FAIL`. It means nobody can tell whether the thing works, which is a worse position than knowing it does not. Usually it means a criterion was written without a check, and the fix is to add one and re-run.

The user can override the gate. If they do, record it: note in the report which criteria were waived and on whose say-so, and carry that note into the archived work order. Do not silently proceed.

---

## Step 4: Confirm the harvest

Only on a full pass, or an explicit override.

Present the Harvest Lens's candidates grouped by destination, and ask before writing anything:

> **Glossary additions** — [term: proposed definition, one line each]
> **Domain model additions** — [entities, relationships, invariants]
> **Non-functional baseline changes** — [what moved, and why it now applies product-wide]
> **Decision records** — [one line each, with the significance test they clear]
>
> Which of these should be promoted? Anything not confirmed stays out.

Nothing is written without agreement. The failure mode here is a glossary quietly filling with terms nobody uses, which is as bad for trust as a stale one.

Be sceptical on the user's behalf:

- **A term belongs in the glossary** only if it carries a meaning specific to this codebase. "User" does not. "Settlement window" might.
- **An invariant belongs in the domain model** only if violating it would be a bug anywhere in the system, not just in this feature.
- **A requirement graduates to `nfr.md`** only if it is now true of the whole product. A bar that applies to one feature stays a delta.
- **A decision earns a record** only if it clears all three significance tests. The Harvest Lens has pre-applied them and named any failing test — trust that and do not argue a candidate back in.

---

## Step 5: Promote into the durable documents

For each confirmed candidate, use the skill that owns the format. **Do not write these files directly** — those skills own their shapes, and restating a format here is exactly the duplication `docs/skill-authoring.md` forbids.

| Confirmed | Do this |
|---|---|
| Glossary terms, entities, relationships, invariants | Invoke `domain-modelling` in append mode with the confirmed additions |
| A decision | Invoke `decision-record` once per decision, passing the context, the alternatives, and why it was contested |
| A baseline change | Amend `docs/product/nfr.md` directly — this skill and `product-definition` share ownership of that file, and the change is a numbered row, not a shape |

Record where each item landed. You need the paths for the `harvested_to` stamp in Step 7.

---

## Step 6: Remove the AGENTS.md entry

Read the repo-root `AGENTS.md` and delete the "Before you start" entry for this work order.

The entry points into `docs/work/YYYY-MM-<slug>/`, which is about to move. Leaving it produces a router that sends agents to a path that no longer exists — worse than no entry at all, because it looks authoritative.

Change nothing else in the file.

---

## Step 7: Archive the work folder

**Never delete.** Move it:

1. Confirm the working tree is clean and the work is committed. If it is not, stop and say so — archiving on top of uncommitted changes makes the move hard to undo.
2. `git mv docs/work/YYYY-MM-<slug> docs/work/_archive/YYYY-MM-<slug>`
3. Stamp every file in the archived folder:

   ```yaml
   status: superseded
   archived: YYYY-MM-DD
   harvested_to: [docs/product/glossary.md, docs/decisions/architecture/0007-webhook-retries.md]
   ```

   Add these keys to existing frontmatter; do not replace it. Files without frontmatter get a block added at the top.
4. Set the archived work order's `status: accepted`.
5. If `docs/work/_archive/README.md` does not exist, create it from `archive-readme-template.md`.

The stamp is what stops an agent finding a superseded specification and treating it as current. It is the one real cost of archiving rather than deleting, and it is worth paying for the ability to get the content back without git archaeology.

---

## Step 8: Report

Write the completed report to `docs/work/_archive/YYYY-MM-<slug>/acceptance-report.md` and present it.

Tell the user:

> "Work order **[title]** is accepted.
>
> - **Verified:** [n] criteria passed[, m waived]
> - **Promoted:** [what went where, as paths]
> - **Archived:** `docs/work/_archive/YYYY-MM-<slug>/`
>
> Nothing was deleted. When you are satisfied nothing was lost:
>
> ```
> rm -rf docs/work/_archive/YYYY-MM-<slug>
> ```
>
> The durable documents are now current as of this work. That is the point of this step — they go stale one unharvested feature at a time."
