---
name: work-breakdown
description: 'Break specified work into a PLAN.md for one work order: numbered, dependency-ordered vertical slices a coding agent executes one at a time, each with its files, acceptance criteria, and exact check commands. The slices are the tickets, reviewed locally before anything reaches Linear or GitHub. Triggers: "break this into stories", "break this into tickets", "create the tickets", "write up the tasks", "generate work items", "work breakdown", "slice this work", "write the PLAN.md". Run ticket-publish afterwards to mirror the slices into a tracker.'
---

# work-breakdown

You are turning a piece of specified work into a `PLAN.md`: a numbered list of vertical slices that a coding agent can execute one at a time, each ending with a green build and a check that proves it.

The plan is the tickets. Each slice is what would otherwise be a story, and `ticket-publish` mirrors slices into a tracker one issue each. No external tool connections are required; everything is written to the local filesystem.

## Routes

Runs on **every route**: it is the one step no route skips. What it reads differs by route. Routes are defined in `../request-triage/references/routes.md`.

**Before you start,** read the work order at `docs/work/WO-NNNN-<slug>/work-order.md` for its ID, route, and the triaged request. If no work order exists, say so and offer to run `request-triage` first. Do not invent one.

**Steps.** If a work order exists, read its `## Steps` before you start. If a line above yours is unticked, name it and ask whether to proceed without it. When you finish, tick your own line and end your hand-off with `Next: <first unticked step>`. The full rule is **Following the Steps** in `../request-triage/references/routes.md`.

---

## Step 1: Establish the input shape

Read the work order's `route` field, then gather what that route produces:

| Route | Read | Produce |
|---|---|---|
| **Direct** | `work-order.md` and the code it touches | A plan with a single slice, `review: stop` |
| **Standard** | `requirements.md` (+ `work-order.md`) | Slices you derive from the requirements, ordered by the dependencies between them |
| **Full** | `delivery-plan.md` + `tech-spec.md` | Slices in the order the delivery plan already set |

If the route says one thing and the files say another (`route: full` with no delivery plan present), trust the files, tell the user which shape you are using, and carry on. Do not stop, and do not fabricate the missing document.

**Check Open Questions** in whichever document is your primary input. If any are unresolved, surface them and ask for answers before continuing. Unresolved questions produce wrongly scoped slices.

---

## Step 2: Propose the slices

Derive the slice list, then present it to the user before writing anything.

**What a slice is.** One vertical change that leaves the build green: everything needed to make that piece work (UI, server, data), not one layer of it. The exception is foundational work with no user-facing outcome (a migration, CI, provisioning a third-party service), which comes first and says so in its goal.

**Where the slices come from:**

- **Full**: each item in the delivery plan's Full Story List becomes a slice, in the plan's order. Merge two items into one slice where neither can ship without the other.
- **Standard**: build the list from the requirements document. One slice per coherent, independently shippable piece. Order by dependency.
- **Direct**: one slice. Skip the confirmation below and go to Step 3.

**Sizing.** Each slice must fit one agent session. If you count more than about eight slices, or find groups of slices that could merge independently of each other, the work order is too big: recommend splitting it, with each part going back through `request-triage` for its own ID. On Full, the delivery plan's phases are the natural split line. Say this before writing anything rather than producing a plan nobody can run in one go.

**Review mode.** Propose one per slice:

- `review: stop` for the first slice, and for any slice that touches a data model, a public API, authentication, or money
- `review: continue` for slices whose shape is settled and mechanical

The user overrides in review.

Present each slice as its number, goal, review mode, and what it depends on if not simply the slice before. Ask:

> "Does this breakdown look right? Add, remove, merge, or reorder anything before I write the plan."

Wait for confirmation before proceeding.

---

## Step 3: Determine the output location

Write the plan into the work order's folder:

```
docs/work/WO-NNNN-<slug>/PLAN.md
```

Use the ID and slug from the work order. Placement and legacy layouts are covered in `../request-triage/references/artefacts.md`; check the host project's `AGENTS.md` or `CLAUDE.md` first.

If no project folder is connected, ask the user where to save it.

If a `PLAN.md` already exists, ask the user whether to overwrite it or cancel before proceeding.

---

## Step 4: Write PLAN.md

Read `templates/plan-template.md` and fill it. Copy the How to run section as it stands, with the slice count, ID, and slug filled in. Keep rule 7 on the Direct route only and delete it on the others, where `acceptance-review` archives the folder instead. How to run is the contract with whichever agent executes the plan, and an agent that improvises is the failure it prevents.

**Context, Goal, Done when.** Keep the *why* in the plan. The requirements document and tech spec are archived at acceptance, so a plan that only links to them loses its context. Write enough that an implementer can make a judgement call the tasks did not anticipate. Link only to **durable** documents: the glossary, domain model, `nfr.md`, decision records.

**Each slice**, drawing on the richest source this route produced:

| | Acceptance criteria | Tasks |
|---|---|---|
| **Full** | The tech spec's test strategy | The tech spec's frontend, backend, and data sections |
| **Standard** | The requirements document's acceptance criteria | The functional requirements, decomposed |
| **Direct** | The work order, plus what the codebase makes obvious | Whatever the change actually requires |

**Do not invent acceptance criteria** on the Full or Standard routes. They are the contract `acceptance-review` verifies against later, and inventing them means verifying the work against a standard nobody agreed to. If the source is silent on a criterion you think matters, add it and say you added it.

**Every acceptance criterion needs its `Verify:` line.** Carry it across from the tech spec. Where a criterion arrives without one, bind it to a check yourself or mark it `Verify: manual — [what a person does and what they should see]`. These are what `acceptance-review` runs when the work order is closed out.

**Files and Do not touch.** Take paths from the tech spec's Files & Boundaries section where there is one, otherwise from the codebase itself. Real paths, not component names. If you cannot name the files a slice touches, the slice is not specific enough to hand over: say so rather than leaving the list empty. Name under Do not touch anything an implementer might reasonably think is in scope and is not.

**Check.** Exact commands, copied verbatim from the tech spec's Verification Commands block, or from the repo-root `AGENTS.md` where there is no tech spec. Do not guess at a package manager or a script name: check `package.json`, the `Makefile`, or whatever the project actually uses. A wrong command is worse than an absent one, because it will be run and believed. A slice whose result can only be judged by eye says so in its Check, with what to look at.

Fill the **Branch** line in How to run, following **Branch names** in `../request-triage/references/artefacts.md`. Before anything is published that is the work order's ID and slug (`WO-0004-csv-export`).

Set the frontmatter `status: draft`, and leave each slice's `Issue:` line and the `tracker:` field as the template has them. `ticket-publish` fills them in.

---

## Step 5: Emit or update AGENTS.md

This is the last step before the code gets written, which is exactly when an agent router earns its place. It runs on every route.

**Guardrail: keep AGENTS.md lean.** An over-stuffed AGENTS.md reduces agent task success and adds cost. This step adds only what is non-inferable: a pointer to this work order's plan. No procedures, no style guides, no explanations; those belong in skills or linked docs.

Check whether an `AGENTS.md` exists at the repo root.

**If it does not exist:** read the template at `agents-template.md` and create it. Fill in:
- The "What this is" line from `docs/product/vision.md` if it exists, or a one-line summary from the work order
- A "Before you start" entry for this work order
- Build, test, and run commands if they are known

Then, if there is no `CLAUDE.md` at the repo root, create it as a symlink so both names load the same file: `ln -s AGENTS.md CLAUDE.md`. If a `CLAUDE.md` already exists as a real file, leave it alone and tell the user the two now overlap.

**If it already exists:** read it, then append or update only the "Before you start" entry for this work order. Do not touch any other section. One line per work order:

```
- Working on WO-NNNN: [Title] → docs/work/WO-NNNN-<slug>/PLAN.md, one slice at a time
```

Never point into `docs/work/_archive/`. `acceptance-review` removes this entry when it archives the folder, so no entry outlives what it points at.

Tell the user:

> "AGENTS.md updated with a pointer to this plan. Keep the file to roughly one screen; if it grows past that, the overflow belongs in a skill or a linked doc, not here."

---

## Step 6: Present the output

Present the plan's path and its slice list.

Tell the user:

> "The plan is saved to `docs/work/WO-NNNN-<slug>/PLAN.md`. Review and edit it before anything runs: renaming, merging, or reordering slices costs nothing at this stage. To build, point an agent at the plan; it runs one slice at a time under the How to run rules. If the slices should exist as tracker issues, run `ticket-publish` first."

---

## Finally: tick your step

If there is a work order, tick `work-breakdown` in its `## Steps` and end with `Next: <first unticked step>`, or say the work order is complete if nothing is left. Change nothing else in the work order.
