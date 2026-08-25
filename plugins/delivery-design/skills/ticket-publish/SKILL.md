---
name: ticket-publish
description: 'Publish locally generated ticket files to a connected project management tool — Linear or GitHub Issues — creating the Epic, each Story, and their blocking relations. Use after work-breakdown has written the ticket files and you have reviewed them. Triggers: "publish the tickets", "push these to Linear", "create GitHub issues from the ticket files", "upload the tickets". Not for publishing artifacts, notes, packages, or posts.'
---

# ticket-publish

You are publishing locally generated ticket files to the user's connected project management tool.

This is step five of the feature pipeline:

1. **backlog-refinement** — idea → PRD
2. **technical-design** — PRD → Technical Specification
3. **delivery-planning** — PRD + Tech Spec → Delivery Plan
4. **work-breakdown** — Delivery Plan → local ticket files
5. **ticket-publish** (this skill) — local ticket files → Linear or GitHub Issues

---

## Step 1: Read the ticket files

Read all files in the `tickets/` subfolder. When a project folder is connected, this is:

```
docs/features/<feature-slug>/tickets/
```

If no project folder is connected, ask the user where the ticket files are saved.

Identify:

- The Epic file (`_epic.md`)
- All Story files (all other `.md` files)

Parse the frontmatter of each file to extract structured metadata:
- `ticket_type` — `epic`, `story`, or `technical`
- `phase` — the delivery phase number
- `depends_on` — list of story filenames this story is blocked by
- `epic` — the epic filename this story belongs to

If the `tickets/` folder is empty or missing, tell the user and stop.

---

## Step 2: Detect the project management tool

Check which project management MCP is connected. Supported tools:

- **Linear** — use if the Linear MCP is available
- **GitHub Issues** — use if the GitHub MCP is available

If both are connected, ask the user which to use. If neither is connected, tell the user and stop — this skill requires a connected tool to create tickets.

---

## Step 3: Confirm the destination

Ask the user:

> **Which project / team / repo should these tickets be created in?**

For Linear: ask for the team name or identifier.
For GitHub: ask for the repository (owner/repo).

---

## Step 4: Present the publish plan

Present a summary of what will be created:

- The Epic title
- Each story title, type (Story or Technical), phase, and dependencies
- The total count

Ask: "Ready to publish these [N] tickets to [tool]? This cannot be undone easily — make sure the files look right first."

Wait for explicit confirmation before proceeding.

---

## Step 5: Create the Epic

Read the `_epic.md` file. Create the Epic in the project management tool.

**Linear**: Create an Issue of type Epic (or the equivalent in the connected workspace) in the specified team.
**GitHub Issues**: Create a Milestone in the specified repository.

Use the markdown body of `_epic.md` (excluding frontmatter) as the description. Use the `# Epic: [Title]` heading as the ticket title.

Save the Epic ID or URL — you will link each Story to it.

---

## Step 6: Create the Stories

For each story file, create a Story ticket linked to the Epic.

Use the markdown body (excluding frontmatter) as the ticket description. Use the `# [Story Title]` heading as the ticket title.

Apply labels based on frontmatter:
- `ticket_type: technical` → label the ticket as technical
- `phase` → add a phase label (e.g. `phase-1`, `phase-2`)

Create stories in phase order — `phase: 1` Technical stories first, then feature stories, then higher phases.

Save each ticket's ID or URL mapped to its filename — you will need this when linking dependencies.

---

## Step 7: Link dependencies

Once all tickets are created, update dependency links between stories using the tool's native relations.

For each story with a non-empty `depends_on` list:
- Look up the ticket ID for each filename listed in `depends_on`
- Create a "blocked by" relation from this story to each dependency

**Linear**: Use issue relations (blocks / blocked-by).
**GitHub Issues**: Add a linked issue comment if native blocking relations are not available.

---

## Step 8: Confirm and summarise

Present the user with:
- A link to the Epic
- A list of all created Story links, in phase order
- The dependency order (which stories to start first)

Tell the user:

> "All tickets are created. Phase 1 stories marked Technical should be picked up first — they unblock everything else. When you are ready to execute a story, pass it to an agent with the relevant codebase context."

---

## Tool field mapping

| Story field | Linear | GitHub Issues |
|---|---|---|
| Epic | Parent issue (Epic type) | Milestone |
| Story type | Issue type: Story | Label: `story` |
| Technical type | Issue type or label: `technical` | Label: `technical` |
| Phase | Label: `phase-1`, `phase-2`, etc. | Label: `phase-1`, `phase-2`, etc. |
| Dependencies | Issue relations: blocks / blocked-by | Linked issues (with comment if needed) |
| Description | Issue body | Issue body |
