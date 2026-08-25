---
name: work-breakdown
description: 'Break a Delivery Plan into an Epic and Story ticket files saved locally as Markdown for review before anything is created in a project management tool. Each story carries its tasks, dependencies, and acceptance criteria. Triggers: "break this into stories", "create the tickets", "generate work items", "write up the tasks", "work breakdown", "turn this plan into tickets". Run ticket-publish afterwards to push the reviewed files to Linear or GitHub Issues.'
---

# work-breakdown

You are generating Epic and Story ticket files from a Delivery Plan and saving them locally for review.

This is step four of the feature pipeline:

1. **backlog-refinement** — idea → PRD
2. **technical-design** — PRD → Technical Specification
3. **delivery-planning** — PRD + Tech Spec → Delivery Plan
4. **work-breakdown** (this skill) — Delivery Plan → local ticket files
5. **ticket-publish** — local ticket files → Linear or GitHub Issues

No external tool connections are required. All output is written to the local filesystem.

---

## Step 1: Receive and validate the Delivery Plan

Accept the Delivery Plan as a file, pasted content, or linked note. Also read the Technical Specification if available — it provides the implementation detail (acceptance criteria, API contracts, data model) needed to write rich story bodies.

Check the **Open Questions** section of the Delivery Plan. If it contains unresolved questions, surface them to the user and ask for answers before continuing. Unresolved questions produce incorrectly scoped tickets.

---

## Step 2: Review the story list

Read the **Full Story List** table from the Delivery Plan — this is the authoritative list of what to generate. Present it to the user:

- The Epic title
- Each story title, its type (Story or Technical), its phase, and its dependencies
- The total count

Ask: "Does this breakdown look right? Add, remove, or rename anything before I generate the files."

Wait for confirmation before proceeding.

---

## Step 3: Determine the output location

Derive the feature slug from the feature name — lowercase, kebab-case (e.g. `user-authentication`, `billing-portal`).

If the user has a project folder connected, save all ticket files to:

```
docs/features/<feature-slug>/tickets/
```

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

Replace the template placeholders with content drawn from the Delivery Plan and PRD.

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

For each story:
- Pull acceptance criteria from the Tech Spec's test strategy — do not invent them
- Pull implementation tasks from the Tech Spec's frontend, backend, and database sections
- Include only the task sections that are relevant (omit Database if no schema changes are required)
- `depends_on` must use the filename of the blocking story (e.g. `create-likes-table.md`), not the title

Generate stories in the order specified by the Delivery Plan — Phase 1 Technical stories first, then feature stories in phase order.

---

## Step 6: Present the output

Once all files are written, present the full list of generated files to the user.

Tell the user:

> "All ticket files are saved to `tickets/`. Review and edit them before deploying — you can rename files, adjust tasks, or change acceptance criteria at this stage with no cost. When you are ready to create the tickets in Linear or GitHub, run the `ticket-publish` skill."
