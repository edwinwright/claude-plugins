---
name: work-breakdown
description: 'Break a Delivery Plan into an Epic and Story ticket files saved locally as Markdown for review before anything is created in a project management tool. Each story carries its tasks, dependencies, and acceptance criteria. Triggers: "break this into stories", "create the tickets", "generate work items", "write up the tasks", "work breakdown", "turn this plan into tickets". Run ticket-publish afterwards to push the reviewed files to Linear or GitHub Issues.'
---

# work-breakdown

You are decomposing a piece of specified work into Epic and Story ticket files, saved locally for review.

No external tool connections are required. All output is written to the local filesystem.

## Routes

Runs on **every route** — it is the one step no route skips. What it reads differs by route. Routes are defined in `../request-triage/references/routes.md`.

**Before you start,** read the work order at `docs/work/YYYY-MM-<slug>/work-order.md` for the route and the triaged request. If no work order exists, ask which route this is and proceed; do not invent one.

---

## Step 1: Establish the input shape

Read the work order's `route` field, then gather what that route produces:

| Route | Read | Produce |
|---|---|---|
| **Direct** | `work-order.md` only | A single story. No epic, no phases. |
| **Standard** | `requirements.md` (+ `work-order.md`) | An epic and its stories. Derive the ordering yourself from the dependencies between them. |
| **Full** | `delivery-plan.md` + `tech-spec.md` | An epic and its stories, in the phases and order the delivery plan already set. |

If the route says one thing and the files say another — `route: full` with no delivery plan present — trust the files, tell the user which shape you are using, and carry on. Do not stop, and do not fabricate the missing document.

**Check Open Questions** in whichever document is your primary input. If any are unresolved, surface them and ask for answers before continuing. Unresolved questions produce incorrectly scoped tickets.

---

## Step 2: Review the story list

Derive the list of stories to generate, then present it to the user before writing anything.

- **Full route** — the **Full Story List** table in the Delivery Plan is authoritative. Use it as it stands.
- **Standard route** — build the list yourself from the requirements document. One story per coherent, independently deployable slice. Each story must include everything needed to make that slice work — frontend, backend, data — not one layer of it. Foundational work with no user-facing outcome (migrations, CI, third-party provisioning) is labelled `[Technical]` and comes first. Order by dependency: anything blocked names what blocks it.
- **Direct route** — one story. Skip the confirmation and go to Step 3.

Present the Epic title, each story with its type and dependencies, and the total count.

Ask: "Does this breakdown look right? Add, remove, or rename anything before I generate the files."

Wait for confirmation before proceeding.

---

## Step 3: Determine the output location

Derive the feature slug from the feature name — lowercase, kebab-case (e.g. `user-authentication`, `billing-portal`).

Save all ticket files into the work order's folder:

```
docs/work/YYYY-MM-<slug>/tickets/
```

Use the slug and date from the work order. Placement and legacy layouts are covered in `../request-triage/references/artefacts.md`; check the host project's `AGENTS.md` or `CLAUDE.md` first.

If no project folder is connected, ask the user where to save them.

The file naming convention is:
- Epic: `_epic.md` (underscore prefix so it sorts first)
- Stories: kebab-case of the story title, e.g. `user-authentication.md`, `create-likes-table.md`

If a `tickets/` folder already exists with content, ask the user whether to overwrite or cancel before proceeding.

---

## Step 4: Generate the Epic file

Read `templates/epic-template.md`. Generate `_epic.md` using the template.

Add the following frontmatter to the top of the file:

```yaml
---
ticket_type: epic
feature: [feature name]
stories: [list of story filenames, e.g. user-authentication.md]
---
```

Replace the template placeholders with content drawn from whichever documents this route produced — the delivery plan and requirements document on Full, the requirements document alone on Standard.

**On the Direct route, skip this step.** One story does not need an epic over it.

---

## Step 5: Generate the Story files

Read `templates/story-template.md`. For each story in the confirmed list, generate a `.md` file using the template.

Add the following frontmatter to the top of each file:

```yaml
---
ticket_type: story | technical
phase: [phase number, e.g. 1]
depends_on: [list of story filenames this story is blocked by, or empty list]
epic: _epic.md
---
```

For each story, draw the content from the richest source this route produced:

| | Acceptance criteria | Implementation tasks |
|---|---|---|
| **Full** | The Tech Spec's test strategy | The Tech Spec's frontend, backend, and database sections |
| **Standard** | The requirements document's acceptance criteria | The requirements document's functional requirements, decomposed into tasks |
| **Direct** | The work order, plus what the codebase makes obvious | Whatever the change actually requires |

**Do not invent acceptance criteria** on the Full or Standard routes — they are the contract `acceptance-review` verifies against later, and inventing them means verifying the work against a standard nobody agreed to. If the source document is silent on a criterion you think matters, add it to the story and say you added it.

Also:
- Include only the task sections that apply (omit Database if no schema changes are required)
- `depends_on` must use the filename of the blocking story (e.g. `create-likes-table.md`), not the title
- Set `phase: 1` on every story when there is no delivery plan to phase them

Generate stories in dependency order — Technical stories that unblock others first, then feature stories. On the Full route, that order is the Delivery Plan's; on Standard, it is the one you derived in Step 2.

---

## Step 6: Emit or update AGENTS.md

This is the last step before the code gets written, which is exactly when an agent router earns its place. It runs on every route.

**Guardrail: keep AGENTS.md lean.** An over-stuffed AGENTS.md reduces agent task success and adds cost. This step adds only what is non-inferable: a pointer to this work order's documents. No procedures, no style guides, no explanations — those belong in skills or linked docs.

Check whether an `AGENTS.md` exists at the repo root.

**If it does not exist:** read the template at `agents-template.md` and create it. Fill in:
- The "What this is" line from `docs/product/vision.md` if it exists, or a one-line summary from the requirements document
- A "Before you start" entry for this work order
- Build/test/run commands if they are known

**If it already exists:** read it, then append or update only the "Before you start" entry for this work order. Do not touch any other section. One line per work order.

Point the entry at the work folder, and name only the documents that exist on this route:

```
- Implementing [Title] → docs/work/YYYY-MM-<slug>/requirements.md + tech-spec.md
```

On the Direct route, point at the work order itself. Never point into `docs/work/_archive/` — `acceptance-review` removes this entry when it archives the folder, so no entry outlives what it points at.

Tell the user:

> "AGENTS.md updated with a pointer to this work order. Keep the file to roughly one screen — if it grows past that, the overflow belongs in a skill or a linked doc, not here."

---

## Step 7: Present the output

Once all files are written, present the full list of generated files to the user.

Tell the user:

> "All ticket files are saved to `tickets/`. Review and edit them before deploying — you can rename files, adjust tasks, or change acceptance criteria at this stage with no cost. When you are ready to create the tickets in Linear or GitHub, run `ticket-publish`. When the work is built, run `acceptance-review` to verify it and close the work order out."
